---
layout: post
title: Orienteer's Origins
categories: XAML, Orienteer
published: true
---
---excerpt
I quick look back at my Orienteer journey.
---end

In working through finally publishing Orienteer recently I spent some time looking back over the code, how it came to be and how it had evolved over the last few years.  It was an interesting journey, one I've really enjoyed and one I've learned a lot from.  Here are some of the highlights.

##Phase 1 - Windows 8 and getting mobile

When Windows 8 beta arrived I picked up the SDKs and samples and started learning.  Having a WPF and C# background I went down the C#/XAML path, rather than WinJS.

I dug around in a number of the samples and went looking for something I could build.  Not because I really wanted to publish an app, what I was looking for was something I could use as a learning tool.  A learning tool not just for me, but something I could use to illustrate concepts and features to others.

What I wanted was something (a domain) people could understand quickly and easily but still allow me to incorporate as many of the features Windows 8 had to offer.

Like many others I know, I like to listen to music while I'm working.  So one day I started up the Music app that came with Windows 8 and found it a bit frustrating to use.  If there was a way to do a playlist, I couldn't find it (remember this was in the days of the Beta, the app is a bit different now).

After a short time messing around with it I decided this was the type of app I'd been looking for.  It ticked all of the following boxes for things I wanted to learn more about:

- Reading/writing files
- Rendering grouped lists, with semantic zoom
- Touch interactions/manipulations
- Tiles and pinning
- Settings
- Background execution
- Search 

##Phase 2 - Grooving along

So with great enthusiasm I set off building my imaginatively named Jukebox application, and in not too long had the core features roughly working and I could list and play the music in my music library.  After a few months of tinkering in my spare time I had working examples of all of the things on my list.

##Phase 3 - First an app, then a framework

This was the point at which Orienteer was born.  Throughout the development of the app I had built and used controller based navigation, because that's what I liked from my WPF days and time spent with the Magellan Navigation Framework.  In order to be able to reuse this code I started refactoring it out, dare I say "into a framework".

As a quick aside, Orienteer had a different name at that stage.  I originally called it Slab, and it was originally going to contain far more than it does today.  For example, it originally contained the PresentationBus, before I pulled that out and made it into its own library.

##Phase 4 - "Why not have both?"

During the evolution of Orienteer I also started doing mobile development for Windows Phone 8, and iOS and Android, using Xamarin and Xamarin Forms.  So the question I then had was "Do I stick with Windows 8 (tablet) or do I go mobile?".  Given that Windows 8, Windows Phone 8 and Xamarin Forms are all XAML based, it turned out to be a case of "Why not have both?".

## Windows 8 Jukebox
I've recently opened up the source code for the Jukebox app, you can find it [on GitHub](https://github.com/slewis74/Jukebox).  In a future post I'll go through each of the feature bullets points from above and talk about implementation specifics.

##Reflections
So did I get what I wanted out of my journey with Orienteer?  Yes, absolutely.

I learned a lot about Windows 8, mobile development in general and the satisfaction of publishing something of your own.

A couple of things that stand out.

**Publish early!**

The core code for Orienteer was "almost ready" for a long time, but I was hesitant about releasing it. I think this was partly due to the origins in Windows 8.  With the lacklustre uptake of Windows 8 I started questioning whether Orienteer would be viable.  The inclusion of the other mobile platforms put those doubts to rest, but the delay cost me time in getting feedback and input from others.

**Do what you enjoy**

Yes, it sounds very cliche, but building Orienteer (and even the sample apps for that matter) has been something I've really enjoyed. Being the Product Owner is also a great learning experience.

##Looking forward...

To Windows 10!