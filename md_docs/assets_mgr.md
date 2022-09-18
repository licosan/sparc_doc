# 5. Assets manager
The assets manager has three purposes:

1. It take care of the loading or pre-loading of your assest like images, styles, fonts, sounds, html fragments (or templates), or json fragments.

2. It takes care of caching: the same asset is never loaded twice as long as you stay in the app, but as soon as the app is reloaded, everything is reloaded (no more browser-cache surprises).

3. It provides helper methods adapted to each asset type, to help you use them.

```text
Auto-preloading:
In many situations, the assets manager will in fact be used automatically by the dependency 
system, to preload what you need son you don't need to worry about it.
However, Asstes manager also comes with a number of helper methods that simplify the way 
you'll use the asset once it is pre-loaded.
They are detailed below together with their respective preloading method.
```

### General remarks for all assets manager's methods:


1. **All Assets manager methods share these parameters :**

    `name` : **String [Mandatory]** the name of the asset to be loaded. If the file extention is absent, the typical one will be added.
    It should not contain the path to the file.

    `path` : **String [Optional]** The path to the file, if you are using subfolders below the respective assets folders.  
    If absent, the file is loaded directly at from the ***/app/assets/[asset-type]/[name]***

    `refresh` : **Boolean [Optional]** If present and true, forces the asset to be reloaded, even if it was already preloaded.

    `callback` : **Function [Optional]** If present and callable, it will be called as soon as the asset is loaded and available. (Thus immediately if the assets was already preloaded).  
    The callabck argument is always the instance of the loaded asset.
    It will also be called when the assets loading fails, just after the server's response. In this case the callback argument is null (so you know something went bad).  
    Upon such a failure, a warning is also issued at the console.

2. **Pre-loading is not mandatory:**

    All the methods of assets manager that help you "**use""* asset (as opposed to those just loading it), will be happy if you invoke them on a non-already-loaded asset.
    In this case, the method in question will just load the asset last-minute for you.
    But then why should you bother declaring them as dependencies ?  
    Well, depending on your server, on the internet connection of the user, and on the weight of the asset, and what you are doing with it,
    the loading time might be preceptible for the user or not.
    For example, an image showing up 500ms late is usually not a big issue, but a sound triggered by a user interaction that starts playing even 300ms late will be clearly perceptible.  


### Available methods for the different asset types:

#### Images assets
`GetImage` : Preloads an image, and returns a TMG Dom node, not attached to the document.  
If the image was already preloaded, the node will be cloned, and the clone returned.

`appendImage` : The argument `selector` **String [Mandatory]**, is used as a traditional Dom selector, and the image is appended **at the end of that node**.  
If the image was not preloaded (manually or via a dependency), it is loaded last minute.


`prependImage` : The argument `selector` **String [Mandatory]**, is used as a traditional Dom selector, and the image is appended **at the beginning of that node**.  
If the image was not preloaded (manually or via a dependency), it is loaded last minute.



#### Fonts assets
`loadFont` : Preloads a font, then creates a [fontFace](https://developer.mozilla.org/en-US/docs/Web/API/FontFace/FontFace) object.
The fontFace is then added to the Dom, and the corresponding node is used in the callback, together with the Font's acronym (=font-familly name).  
If the `name` argument did not include an extension, the extension **'.ttf'** will be used by default (possible extension are '.ttf', '.otf' and 'woff' ).

There are no helper function for fonts. (using them just means using their acronym (=font-familly name) in some styling)


#### Styles assets
`LoadCss` : Loads a CSS file and applies it.

There are no helper function for css. (css references can be used as soon as the css is loaded)


#### Sounds assets
`loadSound` : Preloads a sound. if no file extension is given, '.mp3' is assumed.  
The sound is not played.  
As soon as it is fully-loaded (readyState = 'canplaythrough'), the callback returns the [Audio element](https://developer.mozilla.org/en-US/docs/Web/API/HTMLAudioElement/Audio)

`playSound` : Plays a preloaded sound, from beginning to end.  
One particular sound must finish playing before you play it again (no overlap with itself), but sound can be played while another sound is still playing (overlap between different sounds).

```text
Sound assets used here are just intended to embellish and improve a UI experience. 
They are supposed to be short and meaningfull. 
The assets manager is not made to play long mp3 (like music or so), 
where you'd want to provide some kind controls like pause, rewind, volume etc.
```



#### Json assets
`LoadJson` : Preloads a json file, and returns the corresponding (parsed) objectvia the callback.  
This is made for "Static" Json files (like some config, user defaults,...) that are not meant to change during the app. usage.
Any (even slightly-) dynamic data shall probably come from  your back-end server, via one of your controllers.


#### Html assets
`loadHtml` : Loads an html fragment, or a template.

`replaceByHtml` : Uses an HTML fragment or template as replacement for the inner content of Dom each nodes given by the css selector (given by the `selector` argument).  
If the argument `templateData` is a key-value object, the asset is considered as a template that is processed as a [template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals). 
For more info about templating in Sparc, check the [Views section of "Write your MVC components"](mvc.md) )

`replaceByView` : Same as `replaceByHtml`, but assuming the path starts in ***/app/views***.

`appendHtml` : Creates a Dom element of type `element` argument (defaults to 'div').  
Fills its inner content with the HTML fragment or template.
Then adds this element **at the end** of each Dom nodes given by the css selector (given by the `selector` argument).  
If the argument `templateData` is a key-value object, the asset is considered as a template that is processed as a [template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals). 
For more info about templating in Sparc, check the [Views section of "Write your MVC components"](mvc.md) )

`appendView` : Same as `appendHtml`, but assuming the path starts in ***/app/views***.


`prependHtml` :  Creates a Dom element of type `element` argument (defaults to 'div').  
Fills its inner content with the HTML fragment or template. 
Then adds this element **at the beginning** of each Dom nodes given by the css selector (given by the `selector` argument).   
If the argument `templateData` is a key-value object, the asset is considered as a template that is processed as a [template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals). 
For more info about templating in Sparc, check the [Views section of "Write your MVC components"](mvc.md) )

`prependView` : Same as `prependHtml`, but assuming the path starts in ***/app/views***.


`processTemplate` : Used internally by the methods above tp process the html when it is considered as a template. 
For more info about templating in Sparc, check the [Views section of "Write your MVC components"](mvc.md) )

In case you'd want to use it directly, you'd call it with the followinf arguments:

`name` **String** Template name, just used for console warnings when the pre-flight check fails.

`html` **String** Template content

`templateData` **Key-value Object** used for the evaluation of expressions.

It returns a **String** containing the processed template.

