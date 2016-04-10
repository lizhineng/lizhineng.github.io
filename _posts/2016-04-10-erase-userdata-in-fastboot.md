---
layout: post
title: Erase User Data in Fastboot
categories: android
---

Finally, I resold my old pool Nexus 5 which the screen is broken, here is the solution that how to erase the userdata without using screen.

The first, you need to install android sdk to your computer. If you are using OS X like me, using brew to install. Or, if you are using the others, check out the Android Developer Website.

    brew install android-sdk

The second: connect to the computer and open the terminal, preparing the following command (do not press enter now).

    fastboot reboot bootloader

Then, press the power button on your nexus phone, at the same time, press enter in your terminal.

The last, after the phone has entered into Fastboot, execute the following commands.

    fastboot format userdata
    fastboot format cache

All done, check out your phone now, you will see a whole new android system.
