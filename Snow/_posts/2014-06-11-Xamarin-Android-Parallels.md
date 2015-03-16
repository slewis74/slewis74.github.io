---
layout: post
title: Xamarin, Android and Parallels
categories: Xamarin, Android, Parallels
---
---excerpt
In this post I'll go through some options for debugging Android applications using Visual Studio and Xamarin Studio.
---end
In working on a recent project I tried a couple of different debugging configurations while developing an Android app.

Following is what I learned along the way...

## My setup
Before we go too far, I'm running a Mac Book Pro with Mavericks, Parallels 9 and a Windows 8.1 VM.  

## Android Emulators
I'll jump ahead slightly here to talk emulators.  There are a couple of options available.

The option most popular with others on the recent project team was the [GenyMotion](http://www.genymotion.com/ "GenyMotion") emulator.  It is fast and robust, but it isn't free.

I went with the emulator that comes with the SDK and found it to be quite workable, assuming you have the Intel HAXM working and you don't move it onto your second display ([if you do it will crash next time you start it](https://code.google.com/p/android/issues/detail?id=38371)).

### Intel HAXM Hotfix
There was an issue with earlier versions of Intel HAXM when running in conjunction with Parallels.  The [hotfix](https://software.intel.com/en-us/blogs/2013/11/06/intelr-haxm-hot-ffixes-for-os-x-mavericks-109-and-windows-81-available-for "hotfix") would previously have to be installed manually, but should now be included in the Intel HAXM installer.

You'll know if it's not applied, because when you try to start an x86 based emulator image it'll hang the entire machine.

## Installing the Android SDK(s)
The Xamarin installer will install the Android SDK with a number of defaults.

A lesson I learned quickly was to be selective when installing support for extra versions.  If you simply tick the top level checkbox for a given API level, in the SDK Manager, it will install the SDK plus a number of emulator images, most of which you don't want.  I stuck to installing just the SDK and the x86 emulator image, in OS X.  In the Windows VM I had no intention of running the emulator so only installed the SDK itself. 

## Debugging
Debugging from Xamarin Studio in OS X was straightforward and worked without any real issue or special configuration.

### Preserving data
Depending on your app, you may want to find the option to stop the app data from being cleared on each deploy.  The default setting, in both Xamarin Studio and Visual Studio, will clear all app data from the emulator/device every time you run the app.

To changing the setting in Xamarin Studio:

- Open the Preferences window
- Locate the Projects/Android settings
- Tick the "Preserve data/cache between application deploys"
 
In Visual Studio:

- Open the Tools/Options window
- Locate the Xamarin/Android settings
- Tick the "Preserve data/cache between application deploys"

### Parallels
For part of the project I developed using Xamarin Studio in OS X.  This worked, to a point, but for a number of reasons that I won't go into here I wanted to get back to Visual Studio.

Being new to OS X, Xamarin and Android I came in with a steep learning curve in front of me.  Fortunately [others had already blazed a trail](http://chrisriesgo.com/android-emulator-debugging-from-parallels-to-os-x/ ""), and [with a little extra help](http://stackoverflow.com/questions/1754162/remote-debugging-with-android-emulator "") I was well on the way.  

Some further notes to this:

- When you need the Android SDK path is shown at the top of the Android SDK Manager window 
- ADB ends up in your profile folder, in Windows. For me, the following script worked<br/><br/>
**"%USERPROFILE%\AppData\Local\Android\android-sdk\platform-tools\adb" kill-server** 
**"%USERPROFILE%\AppData\Local\Android\android-sdk\platform-tools\adb" start-server**
**"%USERPROFILE%\AppData\Local\Android\android-sdk\platform-tools\adb" devices**<br/><br/>
- Xamarin Studio may sometimes require a Refresh to see the emulator correctly in the list of available devices.
- Visual Studio must be started after the emulator and all other SSH config.  It will only display the emulators it can see when it first starts.  **If the list changes, you must restart Visual Studio**.
- When configuring the SSH tunnel in PuTTY, the name/IP required can change depending on the network you are connected to.  If the Remote Login settings in OS X read like an IP address then use that IP address, if they don't then use the exact value shown.
- The Remote Login settings in OS X show *username*@*machinename*.  The ***username*@** can also be used in front of the IP/machine name in the PuTTY config, so you don't have to type the admin username each time you connect it, you'll just need to type the password. 