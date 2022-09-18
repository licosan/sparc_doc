# 4. Write your Models, View and Controllers

## Models
```text
Quick reminder :
Models implement the application's dynamic data structures, independent of the user interface. 
They directly manage the data, provide methods that help implement the logic and business rules
of the application.
```

On the client side, they also implement the exchanges with the Back-end (which -in turn- usually talks with a DB of any kind).
In Sparc (like in most MVC frameworks), your models are javascript scripts that must implement a class which inherit from the model base-class (in ***core/baseClasses***)
If you want some "universal" methods to help your models low-level with your servers (like data-caching, lazy-loading, protocols mgt...), 
a good place to put them is in the model base-class.

## Views

```text
Quick reminder :
Views implement any representation of information in text, tables, diagrams, etc. 
Multiple views of the same information are possible.  
Their algorithmic should concern only the (graphical) presentation of data.
```

To implement a view client-side, you need two parts: some HTML, and some javascript that helps shaping it, and integrate data in it.
In Sparc (again like in most MVC frameworks), your views are javascript scripts that must implement a class which inherit from the view base-class (in ***core/baseClasses***)

About the HTML (or template), two approaches are possible (your choice) :

1. Integrated views : the HTML is embed directly within your javascript code. Usually you would use [template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals), by means of backtits, to avoid cumbresome concatenation of strings.

2. Splitted script-HTML : If the HTML fragment is large, it is often more convenient to store it in a separate file, stored alongside the script. 
Sparc lets you do this easily, by using its internal dependency mechanism. (See the 'views' attribute of the controllers config).

Both approaches can of course be combined in a single app, or even in a single view.

> **A word about templating:**
When embedding HTML inside your javascript, you can use template literals. 
In other words, your HTML can include javascript expressions such as ***${x+5}***.
They will be evaluated by javascript, using variables in the current scope,
and replaced by their result. 

> **So what about templating in a "splitted script-HTML" context ?**  
Templates are managed by the Assets manager, and it provides methods to integrate 
your view template into the existing DOM (namely: ***replaceByView, appendByView,  prependByView***).  
One cool feature of these methods is that they accept an argument called ***templateData***.  
***templateData*** must be a key-value object, where the keys are matching your template variables.
If present, it will trigger a genuine javascript-level "template literals" interpretation 
of your template. No new fancy templating engine dependency (your templates are agnostic), no new syntax to learn,
100% backward and forward compatibility with vanilla JS !

> **Fool-proof templating...**  
As explained above, templates interpretation use the genuine vanilla-javascript internal interpretation, but with a small addition:  
In pure javascript, if an expression in the template is wrong (syntax or unknown variable),
**your script will crash.**  
If you use the methods mentionned above, a pre-flight-check is performed on your
template pefore handing it to interpretation: Any wrong expression will be suppressed, a clear warning will be issued at the console,
and **your template will not crash your view !**

> **A word about the path to your templates:**
When using an external template for a view, you need to include your template as a dependecy of that view.  
***There are to possible places for your templates:***
>> some people prefer to have the view class (.js file) alongside with it's corresponding template (usually same name in .html).
This means it will be located somewhere below ***/app/views***
When that is your case,  you need to use the `views` section of the `assets` dependency block, that assumes this particular path, keeping your config small and readable (only view name, or short path relative to ***/app/views***).  
When using Assets helper methods, use those ending with 'View' (namely: ***replaceByView, appendByView,  prependByView***)   

>> Other people prefer to regroup all templates in one place, separated vrom the view classes.  
In that case, you can place them in the assets store at ***/app/assets/html***  
When that is your case,  you need to use the `html` section of the `assets` dependency block, that assumes this particular path.  
When using Assets helper methods, you then use those ending with 'Html' (namely: ***replaceByHtml, appendByHtml,  prependByHtml***)   

>> This difference exists because for `html`, the assets manager (like for any other asset-type) can also cope with any other relative of absolute path or even with external http(s) urls.


## Controllers
```text
Quick reminder : Controllers accept user inputs and converts them to commands (methods)  
for the model or view. It is the bridge between views-logic (graphical behavior), and models-logic (business-logic).
```

In Sparc (again like in most MVC frameworks), your controllers are javascript scripts that must implement a class which inherit from the controller base-class (in ***core/baseClasses***)

**Important** : Controller methods are called with a single argument, which is an object containing any number of named paramerters.  
So a method looks like :
```javascript
    myMethod(args){
        if(args.id != 0) ...
        if(args.coords.x>100)
    }
```

### controllers config.
In Sparc, each controller is accompagnied by a mandatory configuration file.  
It is a json file (.json) placed alongside your controller script (.js) file.  
**It is mandatory**. A controller without config. will not be loaded by the router.  
Everything it contains is optional, but the file and sections must be present.   
A minimal controller config. looks like this:

```json
{ 
  "routes":         [ ],
  "models":         [ "myModel" ],
  "views":          [ "myView" ],
  "dependencies":   [ ],
  "assets":         { "fonts" : [ ],
                      "styles": [ ],
                      "html":   [ ],
                      "views":  [ ],
                      "images": [ {"name":"logo.jpg"} ],
                      "sfx":    [ ],
                      "json":   [ ]
                    }
}

```

The controller config. defines:

- This controller nested routes
- This Controller dependencies:
    - Models dependencies
    - Views dependencies
    - Other dependencies (libs, thirdparties,...)
    - Assets dependencies (images, styles, fonts, sounds, view-templates, other-html-fragments, static-json-files)


#### Nested routess:
They use exactly the same syntax that the op-level routes (see ["Understand routing"](routing.md) )
**But** the url parts are automatically prefixed with the part of the current URL which has led to this controller.

> For example:
Consider the top-level route definition:
```json
    {
        "url": "/module2/user:uid",
        "role": ["*"],
        "controller" : "/module2/userCtrl",
        "method" : "userView"
    },  
```

> Now say that ***"/module2/userCtrl.json"*** goes like:

```json
{ "routes":[ 
        {
            "url": "/edit",
            "role": ["admin"],
            "controller" : "/module2/userEditorCtrl",
            "method" : "launchEditor",
        },     
    ], 
    ...
}
```

> If and URL like ***https://mydomain.com/module2/user/642/edit*** is presented to the router by an ***admin-user***, 
it will end-up in the controller ***/module2/userEditorCtrl***.  
This is because the router was first directed toward the controller ***/module2/userCtrl***, 
but it then added the new rule like ***"url" : "/module2/user/642/edit"*** (notice how the already-matched part is prepended).  
As this new (local) route matches better than the previous one attempted, it wins.

#### Controller dependencies:

Each of the keys `models`, `views` and `dependencies`, expects an array of script names (with or withoout the '.js').  
They can include a path part (like ***"/persons/employees"***) which is respectively below
***/app/models***, ***/app/views*** for `models` and `views`,  and directly below the site root for `dependencies`.  

> As you can see, the `dependencies` is used for any script which is not a model, nor a view.  
You can specify values like ***"/app/libs/xxx"*** for your own app-related libraries, ***"/app/thirdparty/xxx"*** for any thirdparty scripts, or anything somewhere else on your server, or even ***"https://code.jquery.com/jquery-3.6.1.slim.min.js"*** to get an external script (like from a CDN).




