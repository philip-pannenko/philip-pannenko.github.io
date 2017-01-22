---
layout: post
title:  "Hello Wor---, Vaadin!"
date:   2013-10-05 00:00:00 -0500
---

Finally got around to my first technical post. I'm going to document my experiences with Vaadin. I decided to try it out because it's entirely Java and it seems to get take care of a lot of the busy work when putting up a rich interface application online. It could turn out horribly, but it's well documented so I should find out soon. As time goes by I'll think I'll link up an SVN to some actual code.

For now, an obligatory hello world straight from their site:

{% highlight java %}

import com.vaadin.server.VaadinRequest;
import com.vaadin.ui.Label;
import com.vaadin.ui.UI;

@Title("Hello Window")
public class HelloWorld extends UI {
    @Override
    protected void init(VaadinRequest request) {
        // Create the content root layout for the UI
        VerticalLayout content = new VerticalLayout();
        setContent(content);

        // Display the greeting
        content.addComponent(new Label("Hello World!"));
    }
}

{% endhighlight %}