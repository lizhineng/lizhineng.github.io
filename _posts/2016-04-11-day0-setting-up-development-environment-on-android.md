---
layout: post
title: "Day 0: Setting up Development Environment on Android"
categories: code android
---

It's been a long time since I've been wanting to learn Android development. It's no better moment than right now to take action, cause I wanna stop saying "I need to learn Android". This is a 100 days challenge and also a series of recording my learning progress of Android. Each day I will at least post an update about it.

So, here we go. The first thing we need to do is setting the development eviornment.

Launch the Terminal and type the following command, you need to install **brew** first, the platform that I use is Mac OS X 10.11.4, the whole articles in the series are based on it.

    brew install android-sdk

The installation location of Adnroid SDK is

    /usr/local/Cellar/android-sdk/<VERSION>

In this case is

    /usr/local/Cellar/android-sdk/24.4.1_1

Then, launch [**Android Studio 2**](http://developer.android.com/sdk/index.html), find out **Configure > SDK Manager**. Click the Edit button on the *Android SDK Location*.

![Android Studio 2](/imgs/day0-set-up-dev-env-on-android/day0-learning-android-001.png)

We paste the location of Android SDK into it and continue till finish.

Don't close the configure panel, the final step is click **Launch Standalone SDK Manager** on the bottom of the window. When open it up, we don't necessary to install all packages, something we need is:

![Android SDK Manager](/imgs/day0-set-up-dev-env-on-android/day0-learning-android-002.png)

* **Tools**/**Android SDK Tools** *(include all the tools that we need to develop for Android)*
* **Tools**/**Android SDK Platform-tools** *(adb command)*
* **Tools**/**Android SDK Build-tools** *(is used for building your APKs)*
* **Android 6.0 (API 23)**/**SDK Platform**
* **Android 5.1.1 (API 22)**/**SDK Platform**
* **Android 4.4W.2 (API 20)**/**SDK Platform**

If you do not have any Android phones, you'll need to use emulator, some extra packages you need is the system image of every platforms you are going to develop your application and Intel x86 Emulator Accleator(HAXM), it will help you to accelerate the performance of the emulator.

That's it. It's so easy right? See you tomorrow : )
