---
layout: post
title:  "JSF Component Tree"
date:   2014-10-21 00:00:00 -0500
---

**Warning: large code dump**

Ajax and JSF work, but only when the component tree is not large. The reason for this is because every Ajax call, JSF needs to re-render the HTML. When optimizing JSF, rendering times decrease with the use of fewer components.

I ran into a situation where I had many JSF if-statements on a page I was loading. At the time I believed this kept it from rendering in the DOM. It wasn't until my pages started taking a while to load that I paused and took a look at the statements.

Key findings basically included replacing gate logics done in JSF tags to C tags and relying on CSS for other gate-like logic. The reason the C tags worked is because it prevents adding the component to the component tree. I ended up also making static CSS classes that represented different color and rotations of DOM elements instead of using JSF to apply the styling. Here's an lazy code dump example. For those that aren't paying too close attention to the code, this code represents the front-end code for a game called [Carcassonne](https://en.wikipedia.org/wiki/Carcassonne).

**TLDR: minimize your dynamic renders**

{% highlight jsf %}

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:ui="http://java.sun.com/jsf/facelets" xmlns:h="http://java.sun.com/jsf/html" xmlns:c="http://java.sun.com/jsp/jstl/core"
   xmlns:f="http://java.sun.com/jsf/core" xmlns:p="http://primefaces.org/ui" xmlns:o="http://omnifaces.org/ui" xmlns:of="http://omnifaces.org/functions">

<h:head>
   <meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1" />
   <meta http-equiv="X-UA-Compatible" content="IE=edge" />
   <link type="text/css" rel="stylesheet" href="css/main.css" />
</h:head>

<h:body>
   <h:form id="game" prependId="false">

      <!-- Work on the unit panel and the nextTile panel to make it prettier -->
      <h:panelGroup id="toolbar" style="height:200px">
         <c:set var="isGameOver" value="${gameBean.game.gameOverPhase}"/>
            
         <h:panelGroup id="unitDD" >
            <p:graphicImage rendered="#{!isGameOver}" style="z-index: 100;" id="unit" value="images/#{gameBean.game.players[gameBean.game.currentTurn].name}.png" />
            <p:draggable rendered="#{!isGameOver}" for="unit" revert="true" scope="unitDropCell" />
         </h:panelGroup>

         <h:panelGroup id="tileDD" >
            <c:set var="playableTile" value="#{gameBean.game.playableTile}"/>
            <p:graphicImage rendered="#{!isGameOver and !gameBean.game.dropUnitPhase}" style="z-index: 100;" id="tile" value="/images/#{playableTile.image}" styleClass="playerImage rotate-#{playableTile.rotation.degrees}" />
            <p:draggable rendered="#{!isGameOver and !gameBean.game.dropUnitPhase}" for="tile" revert="true" scope="valid" />
         </h:panelGroup>

         <h:panelGroup id="buttons">
            <p:commandButton rendered="#{!gameBean.game.dropUnitPhase and !isGameOver}" immediate="true" action="#{gameBean.rotateTile(true)}" value="Rotate CW" update="tileDD" />
            <p:commandButton rendered="#{!gameBean.game.dropUnitPhase and !isGameOver}" immediate="true" action="#{gameBean.rotateTile(false)}" value="Rotate CCW" update="tileDD" />
            <p:commandButton rendered="#{!isGameOver}" immediate="true" action="#{gameBean.endTurn}" value="Next Turn" update="tileDD unitDD buttons grid score" />
            <p:commandButton rendered="#{isGameOver}" immediate="true" action="#{gameBean.endGame}" value="Calculate Scores" update="grid score" />
            <p:commandButton immediate="true" action="#{gameBean.resetBoard}" value="Reset Game" update="game" />
         </h:panelGroup>
         
         <h:panelGroup id="score">
          <c:forEach items="${gameBean.game.players}" var="player" varStatus="i">
       <p> <img src="images/#{player.name}.png"/> Player: #{player.name} - Points: #{player.points} - Remaining Units - #{player.remainingUnits} #{gameBean.game.currentTurn == i.index and !isGameOver ?  '(Current Turn)' : ''}</p>
   </c:forEach>
            <p>Remaining Tiles: #{gameBean.game.availableTiles.size()} </p>
         
         </h:panelGroup>
      </h:panelGroup>

      <!-- Determine the max array value to use for the columns (so we don't array out of bounds error) -->
      <c:set var="max" value="${gameBean.game.columns-1}" />
      <c:set var="isTileGetUnitDropPanel" value="${gameBean.game.isTileGetUnitDropPanel}"/>
     
      <!-- Build the grid according to the amount of columns needed -->
      <h:panelGrid id="grid" style="border:black;" columns="#{gameBean.game.columns}">
         <c:forEach begin="0" end="#{max}" var="row">
            <c:forEach begin="0" end="#{max}" var="col">

               <!-- Assign the surrounding tiles to variables. Used to minimize the AJAX return from dropping a tile-->
               <c:if test="#{row!=0}">
                  <c:set var="topCellId" value="cell-${row-1}-${col}" />
               </c:if>
               <c:if test="#{row!=max}">
                  <c:set var="bottomCellId" value="cell-${row+1}-${col}" />
               </c:if>
               <c:if test="#{col!=0}">
                  <c:set var="leftCellId" value="cell-${row}-${col-1}" />
               </c:if>
               <c:if test="#{col!=max}">
                  <c:set var="rightCellId" value="cell-${row}-${col+1}" />
               </c:if>

               <!-- Assign this tile and tile's cell id to a variable -->
               <c:set var="cellId" value="cell-#{row}-#{col}" />
               <c:set var="tile" value="#{gameBean.game.tiles[row][col]}" />
     
               <!-- Make the cell. Depending on the situation, it'll be populated with different stuff -->
               <h:panelGroup layout="block" id="${cellId}" styleClass="slot" style="border:solid 1px blue;">
               
               <c:if test="#{gameBean.UIDebug}">
                  <span style="background:white;display:block;">#{cellId}, #{tile.id}</span>
               </c:if>              
             
              <c:choose>
   
                     <!-- If the tile is a droppable spot, make just the droppable spot -->
                     <c:when test="${gameBean.game.validplay[row][col]}">
                        <p:droppable for="${cellId}" tolerance="pointer" activeStyleClass="slotActive" scope="valid">
                           <p:ajax listener="${gameBean.onDrop}" update="tileDD score buttons score ${cellId} ${topCellId} ${bottomCellId} ${leftCellId} ${rightCellId}" />
                        </p:droppable>
                     </c:when>

                     <!-- If the tile has actually been played, render it and any units played on it -->
                     <c:when test="${tile != null}">

                        <!-- Load the image -->
                        <img src="images/${tile.image}" class="rotate-${tile.rotation.degrees}" />

                        <c:set var="imgRow" value="#{tile.ownerCoord[0]}"/>
                        <c:set var="imgCol" value="#{tile.ownerCoord[1]}"/>
                        <c:set var="ownerId" value="#{tile.grid[imgRow][imgCol].ownerId}"/>
                           
                        <!-- Load the individual unit pieces -->
                        <c:if test="#{!empty ownerId and ownerId != -1}">
                           <img class="owner#{imgRow}-#{imgCol}" src="images/#{gameBean.game.players[ownerId].name}.png" />
                        </c:if>

                        <!-- If it's time to place unit for this tile, go ahead and render. This should only be done once per turn.-->
                        <c:if test="${isTileGetUnitDropPanel[row][col]}">
                           <c:set var="isCellGetUnitDropTarget" value="#{gameBean.game.isCellGetUnitDropTarget}"/>
                           <h:panelGrid columns="3">
                              <c:forEach begin="0" end="2" var="unitDropRow">
                                 <c:forEach begin="0" end="2" var="unitDropCol">
 
                                    <c:set var="unitDropId" value="#{cellId}-dp-#{unitDropRow}-#{unitDropCol}" />
                                  <c:if test="#{gameBean.UIDebug}">
                                     <span style="position:absolute;background:white;display:block;top:${unitDropRow*25}px; left:${unitDropCol*25}px;">#{tile.grid[unitDropRow][unitDropCol].id}</span>
                                  </c:if>
                                    <c:set var="currentTile" value="#{gameBean.game.currentTile}"/>
                          
                                    <c:if test="#{isCellGetUnitDropTarget[unitDropRow][unitDropCol]}">
                                       <h:panelGroup layout="block" id="#{unitDropId}" style="top:${unitDropRow*25}px; left:${unitDropCol*25}px;" styleClass="dropCell">
                                          <p:droppable for="#{unitDropId}" tolerance="pointer" activeStyleClass="dropCellActive" scope="unitDropCell">
                                             <p:ajax listener="#{gameBean.onDropUnitPiece}" update="${cellId} tileDD unitDD score buttons" />
                                          </p:droppable>
                                       </h:panelGroup>
                                    </c:if>
                                 </c:forEach>
                              </c:forEach>
                           </h:panelGrid>
                        </c:if>
                     </c:when>
                  </c:choose>
               </h:panelGroup>
            </c:forEach>
         </c:forEach>
      </h:panelGrid>
   </h:form>
</h:body>
</html>

{% endhighlight %}

In hindsight, the application could have been better written in a more static way and using jQuery, but this was meant as more of a learning project. 