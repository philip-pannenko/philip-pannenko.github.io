---
layout: post
title:  "MVP Vaadin"
date:   2013-11-22 00:00:00 -0500
---

**Update (1/31/14):** Found where to place the persistence.xml file. It ended up having to be the Eclipses src root directory, the place where the root of the packages directory exists, ie: {root}/META-INF/persistence.xml.

Got around to posting a little howto to some code I wrote a few months back using the MVP design pattern with Vaadin framework. The code simply authenticates a panel using a login screen and then it allows for some CRUD on an entity.

Specifically, here are some appropriate keywords used:
* Vaadin Navigator
* Eclipse Vaadin Plugin
* Tomcat
* Hibernate
* Hibernate Validation
* MVP Design Pattern

What I liked about this was setup was how easy it was to make basic UI screens. Screens were also very extendable and organized. I can definitely see an easy way to create a nice template in Java and then reference it for every subsequent page needed. Zero CSS/HTML would need to be used or maintained to stand something completely functional, visually appealing and safe on multiple browsers. To speak about the MVP a bit, I was surprised how much extra code needed to be added. It did however split up the implementations very distinctly. Collaborating on an MVP project would be very easy. For a simple application it is a total overkill.

I tried to used JPAContainer however it did not work with my project setup. I used the Eclipse Vaadin Plugin to start my project. The integration was suburb but JPAContainer couldn't pickup the path of the persistence.xml no matter where I placed the xml file. In the future I'm going to make a Maven Vaadin project and see if the JPAContainer works there.

Another issue I had was more of a project organization. I had a single war file packaging all layers together and deployed to Tomcat. This meant that every single time I made a UI change, I needed to update the war which resulted in re-initializing the ORM. No longer was I seeing a fast (less than 1 second) hot deploy, instead it was somewhere around 1 minute per change.

Code Samples:

{% highlight java %}

public class NoteViewImpl extends CustomComponent implements NoteView, View,
        ClickListener, ValueChangeListener {

    private NoteViewListener listener;
 
    @Override
    public void clearFields() {

    }

    @Override
    public void addListener(NoteViewListener listener) {
        this.listener = listener;
    }

}

public interface NoteView {

    public void clearFields()

    public void addListener(NoteViewListener listener);
}

public interface NoteViewListener {

    public void doReset();
}

public class NotePresenter implements NoteViewListener, Serializable {

    private NoteView view;

    public NotePresenter(NoteView view) {
        this.view = view;
        view.addListener(this);
    }

    @Override
    public void doReset() {
        view.clearFields();
    }

}

{% endhighlight %}

Then from a separate class

{% highlight java %}

getNavigator().addViewChangeListener(new ViewChangeListener() {

    @Override
    public boolean beforeViewChange(ViewChangeEvent event) {

        // Check if a user has logged in
        boolean isLoggedIn = getSession().getAttribute("User") != null;
        boolean isLoginView = event.getNewView() instanceof LoginView;

        if (!isLoggedIn &amp;&amp; !isLoginView) {
            // Redirect to login view always if a user has not yet
            // logged in
            getNavigator().navigateTo(LoginViewImpl.NAME);
            return false;

        } else if (isLoggedIn &amp;&amp; isLoginView) {
            // If someone tries to access to login view while logged in,
            // then cancel
            return false;
        }

        return true;
    }

});

{% endhighlight %}