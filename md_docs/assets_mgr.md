# 6. Assets manager
The assets manager has three purposes:

1. It takes care of the loading or pre-loading of your assest like images, styles, fonts, sounds, html fragments (or templates), or json fragments.

2. It takes care of caching: the same asset is never loaded twice as long as you stay in the app, but as soon as the app is reloaded, everything is reloaded (no more browser-cache surprises).

```text
Auto-preloading:
In many situations, the assets manager will in fact be used automatically by the dependency 
system, to preload what you need so you don't need to worry about it.
```

### Available methods for the different asset types:

**All Assets manager methods share these parameters :**

    `name` : **String [Mandatory]** the name of the asset to be loaded. If the file extention is absent, the typical one will be added.
    It should not contain the path to the file.

    `path` : **String [Optional]** The path to the file, if you are using subfolders below the respective assets folders.  
    If absent, the file is loaded directly at from the ***/app/assets/[asset-type]/[name]***

    `refresh` : **Boolean [Optional]** If present and true, forces the asset to be reloaded, even if it was already preloaded.

**All assets manager methods return a promise** that will resolve as soon as the asset is ready, return the asset.


#### Images assets
`GetImage` : Preloads an image.


#### Fonts assets
`loadFont` : Preloads a font, then creates a [fontFace](https://developer.mozilla.org/en-US/docs/Web/API/FontFace/FontFace) object.
The fontFace is then added to the Dom, and is returned upon promise resolve, together with the Font's acronym (=font-familly name).  


#### Styles assets
`LoadCss` : Loads a CSS file and applies it.


#### Sounds assets
`loadSound` : Preloads a sound (The sound is not played).  
The promise resolves only when the sound is completely playable (no streaming).
When resolved, the [Audio element](https://developer.mozilla.org/en-US/docs/Web/API/HTMLAudioElement/Audio) is returned.

`playSound` : Plays a preloaded sound, from beginning to end.  (loads it first if not yet preloaded.
One particular sound must finish playing before you play it again (no overlap with itself), but sound can be played while another sound is still playing (overlap between different sounds).

```text
Sound assets used here are just intended to embellish and improve a UI experience. 
They are supposed to be short and meaningfull. 
The assets manager is not made to play long mp3 (like music or so), 
where you'd want to provide some kind controls like pause, rewind, volume etc.
```



#### Json assets
`LoadJson` : Preloads a json file, and returns the corresponding (parsed) object upon resolve.  
This is made for "Static" Json files (like some config, user defaults,...) that are not meant to change during the app. usage.
Any (even slightly-) dynamic data shall probably come from your back-end server, via one of your models.


#### Html assets
`loadHtml` : Loads an html fragment, or a template.

