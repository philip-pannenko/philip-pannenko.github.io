---
layout: post
title:  "Angular Directives & Form Controls"
date:   2014-11-13 00:00:00 -0500
---

I noticed a neat trick with Angular directives. Just recently started using AngularJS but I had a hard time justifying or even understanding the purpose of custom directives.

Until now I would retype each form control. That would include the styling, the labeling, the form control itself and some sort of validation. As my app grew, I recognized a ton of duplicated code. So I took a look at how I could manage this better. What I ended up doing is making a directive per common form control. Once I replaced all of my previous form controls with the new directive my duplicated code bloat vanished. As a bonus I was able to refactor all of my form controls from one location if I wanted to. Neat!

{% highlight javascript %}

dir.directive('todoInput',function($compile) {
    
  return {
    restrict: "A",
    require: "^form",
    replace: true,
    link: function (scope, el, attrs, ctrl) {
      var template = 
        '<div class="form-group"> \
          <label for="' + attrs.name + '" class="col-sm-2 control-label">' + attrs.propLabelName + ':</label> \
          <div class="col-sm-6"> \
            <input class="form-control" id="' + attrs.name + '" name="' + attrs.name + '" type="text" placeholder="' + attrs.propLabelName + '" ng-model="' + ctrl.$name + '.' + attrs.propModel + '.' + attrs.name + '" required> \
          </div> \
          <span class="col-sm-4 help-block error" ng-show="(' + ctrl.$name + '.' + attrs.name + '.$dirty || ' + ctrl.$name + '.submitted) && ' + ctrl.$name + '.' + attrs.name + '.$error.required">Required</span> \
        </div>';
      $compile(template)(scope,function(_el){ 
        el.replaceWith(_el); 
      });
    }
  }
});

{% endhighlight %}

So, not really sure on performance of this isntead of manually writing out all of that extra code but I am certain it's less code that is being sent over from the server... Maybe this way is faster overall? I'll get to that step when things start bogging down.

