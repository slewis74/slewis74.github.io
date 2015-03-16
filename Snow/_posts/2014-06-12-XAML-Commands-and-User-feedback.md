---
layout: post
title: Commands not executing, but why?
categories: XAML, Commands
---
---excerpt
So now that you've [embraced commands](/2014/06/xaml-commands/), let's look at how we might provide a slightly better experience for users.
---end
So now that you've [embraced commands](/2014/06/xaml-commands/), let's look at how we might provide a slightly better experience for users.

## But why can't I execute?
In several WPF projects I've worked on we've had the requirement to feedback information to the user about why a button cannot currently execute.  Usually this will be due to some sort of validation condition failing.

Enter a weakness in ICommand.  It provides

	bool CanExecute(object value);

for you to implement logic to determine whether the command can execute, given a parameter value.  The limitation is that you can't provide explanation as to why the command can't execute.

For these scenarios, a base class along the lines of the following has worked well

	public abstract class Command : ICommand, INotifyPropertyChanged
	{
		public event EventHandler CanExecuteChanged;

		public bool CanExecute(object value)
		{
			return Errors.Any() == false;
		}

		public bool HasErrors { get { return Errors.Any(); } }

		public virtual IEnumerable<string> Errors
		{
			get
			{
				return Enumerable.Empty<string>();
			}
		}

		public void Execute(object value)
		{
			if (!CanExecute(value))
				return;
			DoExecute(value);
		}

		protected abstract void DoExecute(object value);

        protected void RaiseCanExecuteChanged()
        {
            var handler = CanExecuteChanged;
            if (handler != null)
            {
                handler(this, EventArgs.Empty);
            }
			NotifyChanged("HasErrors");
        }

		private void NotifyChanged(string propertyName)
		{
			// details intentionally left blank
		}
	}

So now with [a little bit of trickery](http://msdn.microsoft.com/en-us/library/system.windows.controls.tooltipservice.showondisabled(v=vs.110).aspx), you can bind up a button along the lines of

	<Button
		Command="{Binding SomeCommand}" 
		ToolTipService.ShowOnDisabled="True" 
		Content="Disabled Button">
		<Button.ToolTip Visibility="{Binding SomeCommand.HasErrors, Converter={StaticResource BooleanToVisibilityConverter}}">
			<ToolTip>
				<ItemsControl ItemsSource="{Binding SomeCommand.Errors}"/>
			</ToolTip>
		</Button.ToolTip>
	</Button> 

Depending on the application, and the likelihood of a large list of errors, you may also need to extend this to include some fixed sizing of the Tooltip and a ScrollViewer.

## Mobile XAML
In mobile apps you don't have the same options for user feedback, Tooltips don't exist in that world, but the errors are likely to still be useful to the user. I'm still looking for a good solution in the mobile space, so stay tuned. 