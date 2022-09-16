# Assets manager
The assets manager has three purposes:
1. It take care of the loading or pre-loading of your assest like images, styles, fonts, sounds, html fragments (or templates), or json fragments.
2. It takes care of caching: the same asset is never loaded twice as long as you stay in the app, but as soon as the app is reloaded, everything is reloaded (no more browser-cache surprises).
3. It provides helper methods adapted to each asset type, to help you use them.

All Assets manager methods share these parameters :

`name` : **String [Mandatory]** the name of the asset to be loaded. If the file extention is absent, the typical one will be added.
It should not contain the path to the file.

`path` : **String [Optional]** The path to the file, if you are using subfolders below the respective assets folders.  
If absent, the file is loaded directly at from the ***/app/assets/[asset-type]/[name]***

`refresh` : **Boolean [Optional]** If present and true, forces the asset to be reloaded, even if it was already preloaded.

`callback` : **Function [Optional]** If present and callable, it will be called as soon as the asset is loaded and available. (Thus immediately if the assets was already preloaded).  
The callabck argument is always the instance of the loaded asset.
It will also be called when the assets loading fails, just after the server's response. In this case the callback argument is null (so you know something went bad).  
Upon such a failure, a warning is also issued at the console.



##Images assets
`GetImage` : Preloads an image, and returns a TMG Dom node, not attached to the document.  
If the image was already preloaded, the node will be cloned, and the clone returned.

`appendImage` : The argument `selector` **String [Mandatory]**, is used as a traditional Dom selector, and the image is appended **at the end of that node**.  
If the image was not preloaded (manually or via a dependency), it is loaded last minute.


`prependImage` : The argument `selector` **String [Mandatory]**, is used as a traditional Dom selector, and the image is appended **at the beginning of that node**.  
If the image was not preloaded (manually or via a dependency), it is loaded last minute.



##Fonts assets
`loadFont` : Preloads a font, then creates a [fontFace](https://developer.mozilla.org/en-US/docs/Web/API/FontFace/FontFace) object.
The fontFace is then added to the Dom, and the corresponding node is used in the callback, together with the Font's acronym (=font-familly name).  
If the `name` argument did not include an extension, the extension **'.ttf'** will be used by default (possible extension are '.ttf', '.otf' and 'woff' ).


##Styles assets
`LoadCss` : 

##Sounds assets
`loadSound` : 
`playSound` : 

##Json assets
`LoadJson` : 


##Html assets
`loadHtml` : 
`replaceByHtml` : 
`replaceByView` : 
`appendHtml` : 
`appendView` : 
`prependHtml` : 
`prependView` : 


### Dead HTML vs Templating

#### Templating with Template literals

##View templates assets
