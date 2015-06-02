---
layout: post
title: Composing views
categories: XAML, PresentationBus
published: true
---
---excerpt
In case of massive XAML files, break here...
---end

A while back I talked about [XAML and Commands](/2014/06/xaml-commands/), and I mentioned UI decoupling using something like an EventAggregator.  In that post I was focusing on the separation of the command logic into Command objects and how that decouples the UI implementation.  In this post we'll expore some more decoupling ideas.

#View Composition

Something I commonly see in WPF and Xaml, where MVVM has been used, are massive ViewModels.  When you dig into these you'll usually find that there are in fact multiple view models that have all been jammed into the one class.  The tendency seems to come down to 

	1 Page = 1 View = 1 ViewModel.

One of the things that initially seems nice about this arrangement is that we have easy access to everything we need.  There's lots of properties and command objects and all sorts of goodies within easy reach, just begging to be coupled together.  Where it leads though is an unhappy place.

Ok, so we're in an unhappy place, where do we go?  We start to decouple the code and separate out the pieces. Let's say we've got a view that has a search panel, a panel with a list of results and a panel to display the details of whichever item in the list is selected. Each of these 3 concerns should have it's own View (user control) and ViewModel, the page's View and ViewModel are then host to the other 3. 

So this sounds good and we start pulling the code apart, let's start with the search panel. And with that we come to a screeching halt.  If the search code is in a separate ViewModel, how is it going to update the results list that is now in a different class?  We'll hit a similar thing when we split the list and the details for the selected item, how does the details panel know which item is selected? 

What we're seeing here is the difference between a coupled UI and a composed UI. A composed UI, as the name should suggest, is built up of components that are independent.  They do not have direct knowledge of what other components are in use around them, but we need a way for them to interact with the other components.

I liken this to micro-service architecture, where you also have a number of services indirectly interacting with one another.  How do they achieve that interaction?  Commonly through a Service Bus of some kind, i.e. through messaging.  So maybe our ViewModels could interact through message to achieve a similar result?

#Messaging and the PresentationBus
This is the scenario where the EventAggregator comes into play.  Now at this point let's clarify that despite the name, it doesn't actually Aggregate Events.  What it does is broker Events between a Publisher and a number of Subscribers.  I'm not sure on the exact origins of the name, but it's been commonly used for the message brokers in a number of frameworks (e.g. Unity, Caliburn Micro) for a very long time.

As part of several side projects I've been working on, I've also built an implementation of an event broker that I called **[PresentationBus](http://www.nuget.org/packages/Slew.PresentationBus/)** (i.e. a Service Bus for the "Presentation Layer").

In a future post I'll go through some more about PresentationBus and illustrate some more examples of its use.