A while back I talked about XAML and Commands, and I mentioned UI decoupling using something like an EventAggregator.  In this post I want to talk about one of the common causes I've seen for Views and ViewModels becoming coupled and cluttered.

The short description of the issue is that composition is not well enough understood and the Views grow to be enormous and complex.  I liken it to writing an entire application in a single class file.

Thinking about how we get into this position consider how UIs often evolve, especially if there is no clearly defined user experience or UI design in place.  The process often starts with "We need a view that does X", then moves to File/New View and then to throwing controls on the page to get something that functions.  I say functions here because initially the objective is often to see if something works, not whether it looks good and/or is easy to maintain.

There's nothing fundamentally wrong with this approach, but if we were to take that approach with non-UI code we'd usually refactor it once we'd learned from the initial implementation.  This refactoring is what usually gets overlooked in the UI, and so the UI code continues to grow and get more tangled and harder to maintain as we move forward.

#Refactoring the UI
There are some simple techniques that can help make life easier when building the View. 
##Look for Copy/Paste
As with any code, we often grab pieces of UI code and reuse them down the page.  They usually stand out in the code as a repeated pattern when you scan down the file.  In a class we'd start refactoring this code into methods.  In UI code we refactor this code into a User Control.

There a 2 variations on how you can implement a UserControl.  The first is to create a Xaml file with a CS code behind file (what Visual Studio will create for you if you ask for a new User Control) and the second is to just create the CS file with the behavious and properties and then use Control Templating to provide the layout at runtime.

With the former you cannot replace the layout at runtime, so it's useful for situations where you purely want to separate something whose layout would be the same whereever it is used.  You still get a single place to change the layout, but there is only ever one layout.

The latter is something I refer to as a "look-less" control, because it doesn't inherantly have a layout ("look") you get to provide it with a template at runtime, and can customise it if you need to.  So it is useful where you have a standard set of behaviour and but need to change the exact layout slightly each time you use it.

##Look for responsibilities
This type of refactoring is usually harder than looking for a repeated pattern, this is about identifying sections that have a single responsibility and/or can be easily isolated from the remainder of the code.

The refectoring is again to create a User Control, although in this case the Xaml with a CS backing file is pretty much always what you want.  Conceptually though, what we are creating is actually another View, so we'd almost always also have a corresponding new ViewModel for the new View.

#Composition
So what we're doing in the above cases is taking 1 big UI and composing it from a number of smaller pieces.  This doesn't mean that the pieces are completely isolated though, they are still part of a broader picture and have to participate for within that broader scope (the scope being the original View).

#Information flow
Given that we have these various pieces needing to cooperate, let's talk about the options we have to make that happen.

## Dependency Properties and Binding
Dependency Properties are built in to the XAML framework.  They define properties of your control that can have their value bound (as in using a Binding).  You can use a "run of the mill" property, and still be able to set it via XAML, if that property isn't something you would need to bind, however this is generally a pretty rare case.

The downside to this approach is that for the binding to work the UI components need to be able to see each other, which given the refactoring we've been doing isn't always possible.  If the UI components are far enough removed in the visual tree they won't be directly visible to each other.

##Messaging
This is the scenario where the EventAggregator comes into play.  Now at this point let's clarify that despite the name, it doesn't actually Aggregator Events.  What it does is brokers Events between a Publisher and a number of Subscribers.  I'm not exactly sure on the exact origins of the name, but it's been commonly used for the message brokers in a number of frameworks (Unity, Caliburn Micro) for a long time.

As part of several side projects I've worked on, I've also built an implementation of an event broker that I called PresentationBus.  The reasoning being that I think of the events and messaging in terms similar to how we use a Service Bus in Micro Service architecture, but in this scenario we're constrained to the "Presentation Layer".

That's all well and good, but how does it help us in our scenario?  Again, it comes back to similarities with Micro Services architecture.  We have a number of components that we don't want to have direct knowledge of each other, but need to communicate and cooperate together to achieve a goal.

In my next post I'll go through some more about PresentationBus and illustrate some examples of its use.