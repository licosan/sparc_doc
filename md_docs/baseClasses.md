# 4. MVC Base classes
All you models, Views and Controllers inherit from the base classes.
They therefore have a number of useful methods described hereafter.

## The Model base class


## The View base class


## The Controller base class
`loadView` : Loads a view, then calls back *onContentLoaded* once it's ready.
If no *onContentLoaded* is provided, the view's template is added to the body.

`unloadView` : Removes a view from the provided content. ??content??

`loadModel` : Load a model.

`getContentById`: 

`appendHTML` : ? should this not better be in the view base class ?

`processTemplate` : ? should this not better be in the view base class ?
Used to process the html when it is considered as a template. 
For more info about templating in Sparc, check the [Views section of "Write your MVC components"](mvc.md) )

In case you'd want to use it directly, you'd call it with the followinf arguments:

`name` **String** Template name, just used for console warnings when the pre-flight check fails.

`html` **String** Template content

`templateData` **Key-value Object** used for the evaluation of expressions.

It returns a **String** containing the processed template.

