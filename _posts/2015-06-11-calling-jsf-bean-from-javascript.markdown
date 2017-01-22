---
layout: post
title:  "Calling JSF Bean from Javascript"
date:   2015-06-11 00:00:00 -0500
---

I know our days with JSF may be numbered with the proliferation of front end javascript frameworks but I found a neat trick.
 
It’s useful when you need to access JSF Bean method and result from within a javascript method. To make that all work, make a PrimeFaces remote command, give it a name and action. Also give it an oncomplete method and pass it three arguments. Like so:

{% highlight jsf %}

<p:remoteCommand 
 name="someRemoteCommand" 
 action="#{sessionBean.doMethod()}" 
 oncomplete="someRemoteCommandCallback(xhr, status, args)"/>

{% endhighlight %}

On the JSF Bean side first make a function that is within scope. Then inside that function call the RequestContext method 'addCallbackParam' and pass it a key/value pair.

{% highlight java %}
public void doMethod() {
	boolean isThisAweomse = true;
	RequestContext context = RequestContext.getCurrentInstance();
	context.addCallbackParam("isThisAwesome", isThisAwesome);
}

{% endhighlight %}

Back on the javascript side, make a callback method to respond to the success of the remoteCommand component. Make sure the function is created with three parameters. What you’ll be looking for is a status to verify the remoteCommand was a success and then the args object to see what was added to it from the JSF Bean side. Like so:

{% highlight javascript %}
function someRemoteCommandCountCallback(xhr, status, args) {
	if (status == 'success') {
		var isThisAwesome = args.isThisAwesome;
	}
}

{% endhighlight %}

And viola, to call this remote command and have it processed in javascript all you have to do it:

{% highlight javascript %}

<script language="javascript"> someRemoteCommand();</script>

{% endhighlight %}