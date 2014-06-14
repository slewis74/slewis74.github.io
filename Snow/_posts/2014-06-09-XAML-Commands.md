---
layout: post
title: XAML and Commands
categories: XAML, Commands
---
During a recent discussion with some developers new to XAML we got onto the topic of Commands.  During the discussion I went over some experiences from previous projects, with  emphasise on one particular pain point. 

## Where does it hurt?
One of the most common pain points I've come across relates to implementations around the command pattern.  The particular pain comes down to the [receiver](http://en.wikipedia.org/wiki/Command_pattern) being a ViewModel.  This works for simple cases, but can quickly lead to bloating and the ViewModel taking on too many responsibilities/personalities.

What does the manifestation look like?

### DelegateCommand/RelayCommand
Included in many of the XAML/MVVM frameworks is the concept of a delegate command.  This is an implementation of ICommand that takes an Action to execute and a Func<bool> to determine CanExecute.  In the constructor you'll see something like this

    SomeCommand = new DelegateCommand(() => DoSomething(), () => someCondition);

Then throughout your view model you will then find 

	SomeCommand.RaiseCanExecuteChanged();

anywhere there's code that could change the result of the CanExecute condition.

### No commands
A variation on this is used by Caliburn Micro.  What it does differently is that the ViewModel code doesn't use an ICommand derived command object.  It instead relies on methods and properties on the ViewModel, which it then wires to UI triggers and enabled properties.  There's no object to create, so no code in the constructor, but you'll find something like this

	public bool CanDoSomething { get { return someCondition; } }

	public void DoSomething()
	{
		// execute stuff here
	}

Then throughout your view model you will then find 

	NotifyChanged(() => CanDoSomething);

anywhere there's code that could change the result of the CanExecute condition.

### So what's wrong with all this?
As I mentioned earlier, this is all workable for simple scenarios but gets unwieldy pretty quickly as screen complexity/number of commands increases.  I've come into code bases for complex WPF applications that have used this style of commands and it isn't fun, with ViewModels having blown out to thousands of lines of tangled code. 

### Embrace the commands
So my advice, embrace the commands.  Abstract the command functionality away from the ViewModel code and write command objects.

Also, decouple the ViewModel and the Command implementation.  Commands that take a "parent" ViewModel in their constructor and wire themselves to events (think PropertyChanged, ObservableCollection.CollectionChanged etc) will lead to trouble and be hard to test.  I've had much better results through decoupling using an EventAggregator.  I'll save further discussion on decoupling and EventAggretion for another time.  