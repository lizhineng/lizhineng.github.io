---
layout: post
title: "Day 1: My First Android Application"
categories: code android
tags: 100daysofandroid
---

Well, day one. Today, I'll learn how to develop my first Android app. It's a really simple app, but don't worry, I think I'll getting stronger in the near future.

Here is the example which I developed today.

![My First Android App](/imgs/day1-my-first-android-app/day1-mfaa-001.png)

Pretty simple sure, when I touch the screen, the counter will automatically plus one by itself.

In this app, I just only use one UI Component called **TextView**, the main logic code is in the **MainActivity.java** file. I think the code is easy to understand.

{% highlight java %}
public void onClickTextView(View view) {
    // Get the reference to the TextView
    TextView counter = (TextView) findViewById(R.id.counter);

    // Declear the variable, it's a integer
    // Note: We need to use parseInt method to convert string into int
    Integer number =  Integer.parseInt(counter.getText().toString()) + 1;

    // Put the number into the counter
    counter.setText(number.toString());
}
{% endhighlight %}

BTW, all the code in this series are hosted on Github already, you can also checkout [the Github repository here](https://github.com/lizhineng/100DaysOfAndroid).

At last, I'd like to say Java is really a OOP Programming language.
