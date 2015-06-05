---
layout: post
title: Orienteer
categories: XAML, MVVM, Orienteer
published: Draft
---
---excerpt
Navigating in MVVM, which way do I go?
---end

Several years ago I set out into the world of mobile app development.  As a developer focused primarily in the Microsoft space, and having spent a good part of the last 8 years doing WPF development, Windows 8 and Windows Phone 8 apps seemed like a logical place to start.

Over the years I've used numerous frameworks and tools, some of which I've grown to love, and others not so much.  One that I am particularly fond of, which was written by a friend and ex-colleague, is the [Magellan navigation framework for WPF](https://code.google.com/p/magellan-framework/).

The MVC style navigation and the removal of the navigation logic and data retrieval from the ViewModels makes so much sense to me, and when I started off into the mobile XAML world I found I was missing my controllers.  So over the course of the last 2-3years, and several projects, I've been slowly putting together something very similar, but a bit smaller and more mobile focused.  Introducing the result...

#Orienteer
Orienteer is available as a nuget package, and the project page can be found over on GitHub.

##What's in the box?
Let's have a look at some of the core components and features.

###Controllers and the Navigator
The core to navigating is a Navigator.  Any of the commands that need to trigger a navigation simply needs to take a dependency on a Navigator, it provides the gateway to the controller actions.

Controllers are controllers in the MVC, not the iOS sense, for those who've done iOS development.  An action can return a ViewModelResult, in order to navigate to the View (page) associated with that ViewModel.  An action can also return a DataActionResult, which only contains data (ViewModels can access these actions via the GetData methods on the Navigator).

###ViewModel first navigation
As mentioned above, the controller actions can return a result identifying the ViewModel to navigate to, i.e. it uses ViewModel first navigation.  The View is located by naming convention, using a parallel hierarchy search from configured root namespaces for the Views and ViewModels.

So the Views and ViewModels may reside in the same folder, in which case the root namespaces match, or they may reside below separate root level folders.  As an example of the latter, you might have ViewModels/Feature1/SomeViewModel.cs and it corresponding view might be Views/Feature1/SomeView.xaml   

###Routing
Orienteer also supports routing similar to MVC.  For example, we could have a route that looks like the following

	/contacts/search?name=smith

This would call the Search action on the ContactsController, passing a value of 'smith' to the name parameter.

There were 2 reasons for needing this, first is the routes for the current pages in the navigation stack are persisted, if required, so the stack can be restored on a restart of the app.

Second, on Windows Phone and Windows 8 it's used for deep linking from pinned tiles.

###Async support
The Navigator and Controller actions have full support for async/await. So you can NavigateAsync to a Controller action that is awaiting data retrieval.

###Automatic Dispatching
The BindableBase and DispatchingObservableCollection both automatically dispatch all Changed events back onto the UI thread.  So if you have background data loads happening you never have to worry about dispatching.

While we're on DispatchingObservableCollection, it also has support for LargeUpdates.  Once you start a Large update the CollectionChanged events are suppressed until you call complete, at which point a Reset is raised.  This is useful in scenarios where you know there'll be a large amount of items being added and you don't want the performance hit of a re-paint per Add.

###NavigationFrame
In Windows 8 the inbuilt navigation Frame has some interesting behaviour regarding the way it caches or recreates Views when you navigate Back.  To work around this Orienteer has it's own NavigationFrame for Windows 8.

###Xamarin Forms Support
Being XAML based, the navigation concepts that Orienteer uses fit in quite well with Xamarin Forms, so it's supported out of the box.

##Sample Code
To help get going with Orienteer there is are samples included with the source.  At the time of writing this post the Xamarin Forms sample is complete.  Windows Phone and Windows 8 will be coming shortly.

The samples currently all revolve around a simple app to display the music content on your device.  The Windows 8 sample that's coming will also include playing functionality, to help illustrate some more decoupling concepts and some more advanced uses of the **[PresentationBus](http://www.nuget.org/packages/Slew.PresentationBus/)**.

###Windows Phone
If you run the Windows Phone app on the emulator you'll want to connect some music files to the emulator.  I'd recommend using the Windows Phone 8.1 Emulator.  In the SD Card configuration select a folder that contains some Music files and select Insert Card.  You'll get a prompt in the emulator at that point, select Yes and you're away.

Note that the Windows Phone emulator resets its state on each restart, you have to select to Insert Card each time you restart it.

###Android
Again, on the emulator you'll want some music files.  If you open an Android console you can use  

	adb push someMusicFolder /sdcard/

This will copy the files onto the emulator.

###iOS
On the iOS, I haven't found a way to get music onto the emulator as yet.  The MusicProvider class for iOS currently returns some static data by default.  If you're running on a device then commenting out the static data code at the top of the ScanMusicLibraryFolder method.  