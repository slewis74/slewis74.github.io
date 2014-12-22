A while back I talked about XAML and Commands, and I mentioned UI decoupling using something like an EventAggregator.  So in this post I wanted to go through a bit more about decoupling and illustrate some cases where I've found it useful.

#Coupling
Let's start by looking at some examples of the types of coupling I'm talking about.

##List/Detail

This is a common UI layout in many applications.  You have a list of items somewhere on a page, and when you select one it's details are shown on a panel somewhere else on the page.

In XAML this can be achieved pretty easily, if the list view and the panel are both in the same XAML file, and the view model backing the list is the same view model that the panel needs to display.  You might then have something like the following for the panel

	<grid DataContext="{Binding SelectedItem, ElementName=someListView}">
	...
	</grid>

So this works while the markup is simple, and you're willing to have it all in 1 file.

##I just changed something


#Coupling vs Composing

#Messaging

#PresentationBus


#Controllers in the UI