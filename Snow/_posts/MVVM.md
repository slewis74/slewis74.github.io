
#The Theory
##What is a Model?
A Model represents data.  The data may be a single entity, it might be an aggregate of multiple model entities.
It responsibility is the hold the data and publish changes then they occur.

##What is a View Model?
A View Model represents the visual state of the data, and encapsulates the visual behaviour around that data.  It may project the data stored in a model, but it's responsibility is to hold the transient, visual state of the data.  IsSelected, IsChecked, IsEnabled are examples of the types of properties on a ViewModel, they are not persisted but are important to the visualisation of the data.

##What is a View?
A View presents the data to a user, through the use of ViewModels and Models.  A View can use a Model directly, if there is no requirement for visual state.

##Why MVVM?
MVVM is an adaptation of MVP and MVC, adjusted to play to the strengths of XAML's binding abilities.
By isolating the visual behaviour away from the View and into a ViewModel, which is just a class at the end of the day, that behaviour also becomes much easier to unit test.

##Let's get moving
So we've got a View rendered, but what about when the user does something that requires us to navigate to another View?  Within whichever platform and tooling you've chosen there will be some form of Navigator or NavigationService to facilitate this.

With MVVM not being a "first class citizen", the platforms will usually have the Navigator exposed on the View, they don't assume that you have code beyond the View's code-behind. This also allows the UI controls to access it directly.  The frameworks will usually provide an abstraction over this and will take care of pushing it into your ViewModels.

Deep linking...

##Where's my data coming from?
So when we look at the navigation pipeline, the navigator itself is View centric, when you navigate you request a View.  The frameworks then hook in a find a ViewModel for that View.  But where does the Model come from?  Usually it's left to the ViewModel to interact with some other component, e.g. a Repository or Service, to load/retrieve data.

To me, this doesn't feel right.  The ViewModel's responsibility is visual state/behaviour, but now it's doing data retrieval, and before you know it it'll be handling saving data.  And if we're not careful it'll try to take on command behaviour.

I have long been a supporter of a model that I first saw in the Magellan navigation framework.  It uses a model based on ASP.NET MVC, and uses Controllers for navigation.  Collegues and I have taken to referring to this model as MVVM-C, as in MVVM with Controllers.

#Yet another framework
Introducing SLAB.  SLAB is a navigation framework based on MVVM-C, with current support for Windows Store Apps and Windows Phone mobile Apps.  I'm currently looking into Xamarin and Xamarin Forms support.

I've been using a simple Jukebox app as a test case.

#What does a navigation call look like?

    navigator.Navigate<ArtistController>(c => c.ViewArtist("Jack Johnson"));

So in this call, we're invoking the ViewArtist action on the ArtistController, and passing it the artist name that we want displayed.

#Navigation Commands
If you've read ####, then you'll know that I like to abstract Commands into Command objects.  SLAB provides a couple of Command base classes, and NavigationCommand is one of them.

#Deep Linking
A Deep Link is similar to a URL in a web application.  They are used to invoke the app and navigate to a specific page, with specific parameters.  When you create a Secondary (Live) Tile for an app it will usually include a Deep Link.

The out-of-the-box deep links looks something like

	ms-appx://jukebox/...

SLAB supports this format of link, but it also supports a more "MVC" style format as well, e.g.

	ms-appx://jukebox/Artist/ViewArtist?Name=Jack Johnson

