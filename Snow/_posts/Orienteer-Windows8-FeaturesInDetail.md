
#Rendering Grouped lists, and semantic zoom
Grouping lists, with variable sized tiles, was one of the first things I attempted in Windows 8.  I was fascinated, and perplexed, as to why such a core concept to the way tiles are rendered wasn't more "baked in".

The implementation I used in the Jukebox is based on **TODO**.  Logic in the ViewModels actually determines which tiles are going to be which size.  In the current implementation the first (alphabetically) artist in each group on the main page is large, and all of the others are small.  Given the nature of the album covers being square, a medium size rectangular tile wasn't necessary, but making a rectanglular tile (either landscape or portrait) is supported by the implementation.

##Semantic Zoom
The semantic zoom implementation is quite straighforward.  All you need to do is **TODO code snippet**

#Touch interaction/manipulation
I originally implemented some touch manipulation, to see how it worked.  Then wasn't sure about it, so removed it.  Then wanted to demo manipulation, so put it back in.

The manipulation I came up with involves being able to drag a track or an album and drop it on the play button to play it.

#Tiles and pinning

#Settings

#Background execution

#Search
