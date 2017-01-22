---
layout: post
title:  "Debugging Java Beans"
date:   2016-06-15 00:00:00 -0500
---

I have no idea why I didn't realize this earlier. It would have saved me a ton of time.

When debugging a hard to reach Java code that relies on a Bean, I realized it's possible to serialize the Bean and work on it from an isolated environment or unit test. Behold.

Run the following code on the bean you intend to debug.

{% highlight java %}

FileOutputStream fos = context.openFileOutput(fileName, Context.MODE_PRIVATE);
ObjectOutputStream os = new ObjectOutputStream(fos);
// this is the bean you're in
os.writeObject(this); 
os.close();
fos.close();

{% endhighlight %}

Then open up a unit test and load your bean for super quick debugging and development.

{% highlight java %}

FileInputStream fis = context.openFileInput(fileName);
ObjectInputStream is = new ObjectInputStream(fis);
// default serialization requires identical jar
SimpleClass simpleClass = (SimpleClass) is.readObject(); 
is.close();
fis.close();

{% endhighlight %}

You're welcome for the hours of agony I just saved you from having to experience.