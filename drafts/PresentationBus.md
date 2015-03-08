In my last post I talked about View Composition and introduced the PresentationBus as something that help us when building composable UIs in XAML.  In this post I'll use a couple of examples to illustrate how to make use a PresentationBus (or any other EventAggregator for that matter) in your applications.


#Pub/Sub







#What am I calling Tight Coupling?
What I'm really getting at comes back to the SOLID principles, especially Single Responsibility.

What I often see in View implementations are massive files with large amounts of XAML constructed from intrinsic elements (think StackPanel, Grid, TextBlock).  The View will often have lists and detail panels and buttons and menus and all sorts of things all jammed in together.  The knock on effects are that the ViewModel will tend to grow in much the same manner, to reflect its View, and they both take on "global" thinking patterns.  By that I mean they have multiple personalities (responsibilities) but each can see the internal workings of 
the others and it quickly becomes a tangled, tightly coupled mass of code that is hard to maintain and often next to impossible to test.

As with classes, what we really should be doing is breaking the UI down into smaller piece, each with a single responsibility.  Custom controls and UserControls are a good way of dealing with this.


Let's start by looking at some examples of the types of coupling I'm talking about.

##List/Detail

This is a common UI layout in many applications.  You have a list of items somewhere on a page, and when you select one it's details are shown on a panel somewhere else on the page.

In XAML this can be achieved pretty easily, if the list view and the panel are both in the same XAML file, and the view model backing the list is the same view model that the panel needs to display.  You might then have something like the following for the panel that shows the details

	<grid DataContext="{Binding SelectedItem, ElementName=someListView}">
	...
	</grid>

Somewhere else on the page you might have a button to navigate based on the selected item, which might look like this

	<button 
		Command="{Binding OpenItem}"
		CommandParameter="{Binding SelectedItem, ElementName=someListView}"
		Content="Open"/>

This works while the markup is simple, and you're willing to have it all in 1 XAML file.

##Forms
Another common scenario is data entry.  In this scenario we often have a view model that is holding a number of values that are being edited and a Save command that is checking validation.  This leads us to the coupling issues that I highlighted in my earlier post. 

#Loose Coupling
The alternative to tight coupling is, big surprise, loose coupling.  Loose coupling involves building components that can be brought together without knowledge of one another.  They need to communicate, but they don't know or care exactly who they are communicating with.

This isn't an uncommon pattern, we use it frequently for distributed systems and micro-service architecture.  The key to making it work, Messaging.

With micro-services we typically use a service bus for messaging.  Within a rich client application what has traditionally been called an Event Aggregator has been used for this type of messaging.

#PresentationBus
There are several event aggregators commonly in use in the WPF/XAML community (Caliburn.Micro's probably being the most common), which are all quite good and well worth using.  I've also been working on an implementation for a couple of side projects over recent years, but I'm looking at some additional functionality to the others that are out there.

What lead me to this?  Well I came across a couple of situations where I needed to not just Publish events, I needed to Request Responses.  As a quick example, I had a UI where you could drag elements around, and needed to know which other UI components had controls that you could drop the element onto, where they were on the screen and which command object to execute when you did.

Why the name?  Well I've never really thought EventAggregator was a great name to describe what's happening here.  As I outlined above, I think of this more like a bus in micro-service architecture, it's just that the services are all in the UI process (the "Presentation Layer").  So for want of a more imaginative name, I went with PresentationBus.

# How does it work?
Given a bus, let's look at what the code from the above example might look like.

## List/Detail
In our scenario for the List/Detail, we have a list of items being displayed and when you select one of the items we display it in a details panel.  The ViewModel for the list won't change too much, except that it now publishes events.

	public class SomeListViewModel
	{
		...

		public SomeItemViewModel SelectedItem
		{
			get { return _selectedItem; }
			set
			{
				if (_selectedItem == value) return;
				_selectedItem = value;
				OnPropertyChanged(() => SelectedItem);
				_bus.Publish(new SomeItemSelected(_selectedItem));
			}
		}

		...
	}

The event in this case is carrying the details of the selected item, and would be implemented as

	public class SelectedItem
	{
		public SelectedItem (SomeItemViewModel item)
		{
			Item = item;
		}

		public SomeItemViewModel Item { get; private set; }
	}

The ViewModel for the detail panel then indicates that it is a handler of that event

	public class SomeItemPanelViewModel : IHandle<SomeItemSelected>
	{
		...

		public void Handle(SomeItemSelected event)
		{
			// Update the view model's properties based on the event
		}

		...
	}

The instances of the classes that handle events must be registered (subscribed) to the bus in order to actually receive the events.  Whoever creates the instance is responsible for subscribing the instance.  This might sound onerous at first, but if you're using an IoC container it will usually be able to handle this for you.  See the PresentationBus project page for details on an example Autofac setup.

#Scoping