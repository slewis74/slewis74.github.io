---
layout: post
title: Orienteer and Windows Phone
categories: XAML, Orienteer
published: true
---
---excerpt
Orienteer now has support for Windows Phone 8 Silverlight apps!
---end

Orienteer now has support for Windows Phone 8 Silverlight apps, and there's a [sample app](https://github.com/slewis74/Orienteer/tree/master/Samples/WinPhoneSample).

The bulk of the setup code is very similar to that of the Xamarin Forms applications, with the exception of the PhoneApplicationFrameAdapter.

##PhoneApplicationFrameAdapter
To hook Orienteer into Xamarin Forms we use a custom NavigationPage, to hook in to Windows Phone we use an adapter that attaches to the PhoneApplicationFrame.  The code below shows how to initialize the adapter during the application initialization.

	private IContainer _container;

    private void InitializePhoneApplication()
    {
        if (phoneApplicationInitialized)
            return;

        LetThereBeIoC();

        RootFrame = new PhoneApplicationFrame();
        RootFrame.Navigated += CompleteInitializePhoneApplication;
        RootFrame.NavigationFailed += RootFrame_NavigationFailed;
        RootFrame.Navigated += CheckForResetNavigation;

        InitFrameAdapter();

        phoneApplicationInitialized = true;
    }

    private async Task InitFrameAdapter()
    {
        var adapter = _container.Resolve<IPhoneApplicationFrameAdapter>();
        adapter.PhoneApplicationFrame = RootFrame;
        await adapter.DoStartup();
    }

    private void LetThereBeIoC()
    {
        var builder = new ContainerBuilder();
        builder.RegisterAssemblyModules(new[]
        {
            typeof(App).Assembly
        });

        _container = builder.Build();
    }

###Autofac registration
To register the adapter in Autofac you will need a registration similar to the following

	builder
		.RegisterType<PhoneApplicationFrameAdapter>()
		.As<IPhoneApplicationFrameAdapter>()
		.WithParameter("viewRootNamespace", ViewRootNamespace)
		.WithParameter("baseFeatureFolder", "Features")
		.SingleInstance();

Where:


- ViewRootNamespace is the same namespace used by the ViewLocator
- "Features" is the ViewRootNamespace's folder, relative to the root level of the application.

These values are used by the adapter to determine the correct XAML file to navigate to, when a ViewModelNavigationRequest is published.

###Manifest Navigation Page
In the Windows Phone manifest there is a value for the default page to navigate to.  Orienteer doesn't use this value, it uses the NavigationStack's defaultRoute to determine the default route, and therefore page, to display.  It does however have to understand what the value is.

The reason behind this is that the PhoneApplicationFrame itself will navigate to the specified page upon startup and cause issues with Orienteer's stack monitoring.

You have a couple of options around this value:

1. Set the value to Orienteer, per the WinPhoneSample 
<img src="/images/wp8-manifest-navpage.png" width="360px" height="262px"/>

2. Set the value to be blank, and ignore the red text that appears in Visual Studio's manifest editor.
3. Choose your own custom value. In this scenario you will also need to set assign the custom value to the ManifestNavigationPage property of the adapter, for example in the Autofac registration you'd include something like  


		.WithProperty("ManifestNavigationPage", "MyCustomValue")

 