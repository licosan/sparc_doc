# Sparc file structure

## Root directory :
* `/app`
- This is where your application lives, where you'll do your dev. work.
* `/core`
- This is where Sparc mechanics lives (you should not need to touch anything in here).
* `/index.html`
- This is the only HTML page ever loaded by your browser. These 10 lines of HTML are bootstrapping Sparc. (see the section about configuring your Nginx / Apache for Sparc)

### /core :
You should not have to change anyting in here.
If you do find something helpfull to change in the Core, please consider making a pull-request ;-)
* `/baseClasses`
- Contains model, view, controller and user base classes
* `/helpers`
- Helpers for Sparc itself
* `/libs`
- Libraries for sparc itself. Here lives the Router, the Assets manager, the Logger and the MessageBus.
* `/Sparc-core-1.0.js`
- Sparc bootstrap script. This is also where the Loader lives, as it is immediately needed to load internal (core) libs and MVC base-classes.
* `/utils`


### /app :
* `/assets`
- This folder contains your images, fonts, style-sheets, sounds, static json, and html templates. This default path could be changed via ***assets.basePath*** setting in the application config.
* `/config`
- This folder contains json configuration files.
* `/controllers`
- This folder contains your controllers, eventually in a directory structure. This default path can be changed via ***router.controllersPath*** setting in the app. config.
* `/docs`
- This is where you should document your application...just a siggestion ;-).
* `/helpers`
- Put here any short, reusable piece of script that can be usefull anywhere in your app (any MVC components). Typically, you'll want to define global functions here like a date-conversion function, or enrich javascript-core prototypes, like adding a 'toCurrency' formatting method to the Number type for example.
* `/libs`
- Put here your large scripts, implementing a specific set of features that are not suited in a model, view or controller. Typically, a library defines a class that will be instantiated by models, views of controllers.
* `thirdparty`
- Put here any external libraries that you need. You probably want to git-ignore this folder !
* `/models`
- This folder contains your models, eventually in a directory structure. This default path can be changed via ***router.modelsPath*** setting in the app. config.
* `/views`
- This folder contains your views, eventually in a directory structure. This default path can be changed via ***router.viewsPath*** setting in the app. config.

#### /app/assets :
* `fonts`
- Put here all the fonts (like .ttf, .otf, .woff) that some of your views depends upon.
* `html`
- Put here all the static html fragments you might need in your views. Templating is allowed, using the standard "template literals" syntax of javascript. Template interpretation will activate if anf only if you pass a `templateData` object to the asset helpers functions. 
This is for general-purpose HTML fragments / templates. When your template is uniquely tied to a specific view, you probably want to store it in the views folder, alongside the corresponding javascript file (same name but .html). See the topic "Using Assets manager")
* `images`
- Put here all the images that some of your views depends upon.
* `json`
- Put here any piece of STATIC json that any component might depends upon.
* `sfx`
- Put here all the .mp3 sounds that some of your views depends upon.
* `styles`
- Put here all the .css files that some of your views depends upon.

