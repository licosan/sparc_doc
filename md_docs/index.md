# Welcome to SPARC
```html
         ___  ____   __    ____   ___ 
        / __)(  _ \ /__\  (  _ \ / __)
     ___\__ \ )___//(__)\  )   /( (__     
    (_______/(__) (__)(__)(_)\_) \___)  
    Single Page Applications Rational Code
    
```
## Sparc is a javascript MVC framework for Single Page Applications
### Sparc main objectives

* `MVC` 
    - Model View Controller oriented (yes, Browser side), with a corresponding file structure for your javascript, templates and configuration files.
* `Truly detached` 
    - Your application boots, and serves all its different URLs without the help of a backend server.
Only static files are used. Backend server is only there to answer to AJAX requests.
* `Small footprint, fast serving` 
    - Loads only what you need, just whene you need it: Scripts and Assets ( like images, fonts, css, ...) are loaded only once (clever non-browser caching), only when needed (lazy-loading).
* `Clever Loading` 
    - Scripts can depend on other scripts and on Assets. Define your dependency trees in clear JSON files, and Sparc will make sure everything your script needs is loaded beforehand. It will also optimize the loading by loading several things in parallel, but only where there is no risk to break a dependency.
* `Powerful URL Routing` 
    - Sparc lets you define route patterns in readable JSON files.
    - Routes can be nested: a controller can redefine its own scope of routes.   
    - The URL patterns can use Regular Expressions, to extract named parameters.   
    - Methods can be specified, or not.
    - A best match strategy is used when several routes are matching the URL + User role combination.
    - Forward / Back buttons and internal links are intercepted to avoid any unwanted reload and the user is warned before exiting the application.
* `External error logging`
    - Javascript Warnings and Errors in the console (from your code or directly from the browser) can trigger a fire-and-forget Ajax to any external service to have them store in a database, sent formated emails, etc...
    - The error-level, the stack-trace and all relevant informations (like url, user, user-role) are included in the Json.

