---
layout: post
title: "Android Studio: Unable to Access Android SDK Add-on List"
categories: code android
tags: android
---

![Android Studio: Unable to Access Android SDK Add-on List](/imgs/utaasaol/utaasaol-001.png)

Add the following code to *idea.properties* which is located at the *bin* folder in the installation path of your Android Studio.

That means:

Windows: **C:\Program Files\Android\Android Studio\bin\idea.properties**  
Mac: **/Applications/Android Studio.app/Contents/bin/idea.properties**

    disable.android.first.run=true

![idea.properties file](/imgs/utaasaol/utaasaol-002.png)

Save it and restart the app, it works again.
