# Welcome to SPARC
```text
         ___  ____   __    ____   ___ 
        / __)(  _ \ /__\  (  _ \ / __)
     ___\__ \ )___//(__)\  )   /( (__     
    (_______/(__) (__)(__)(_)\_) \___)  
    Single Page Applications Rational Code
    
```
Sparc is a javascript MVC framework for Single Page Applications.

## Sparc main features

* `MVC` 
    Sparc is a framework with the MVC architectural pattern at its center.  
    MVC has been used extensively for traditional web application, on the server side, for decades in every possible programming language: for example, think about Ruby-on-Rails in Ruby, Django in Python,  CodeIgniter / Zend in PHP, Spring / JSF / Struts in Java just to name a few.
    With the advent of Single Page Applications, the front-end (browser) side of applications now also needs an architectural pattern that can structure large amounts of code, and MVC fits just as well.  
    - Models, Views, Controllers and Routing on the client-side (browser) have the same purpose as they have on the server-side.
    - Sparc implements a corresponding file structure for your scripts (Models, View & Controllers), plus templates and configuration files.

    ** Be aware: ** You should be familliar with the MVC pattern before digging any further into this doc. You'll find plenty of cool introductions on the web if you think you need it.

* `Truly detached` 
    - Your application boots, and serves all its different URLs without the help of a backend servers.
Only static files are used.  
    The backend servers or micro-services are only there to answer to AJAX requests.

* `Small footprint, fast serving` 
    - Loads only what you need, just when you need it: Scripts and Assets (images, fonts, css, ...) are loaded only once (clever non-browser caching), only when needed (lazy-loading).  
    - **Load few** and small components from the server, 
    - Have a **small footprint** in the bowser memory, and minimize the risk of leaks (important as an SPA can run for hours without reloads)
    - **Minimal code exposure:** Expose to he user only the javascript code that this user needs and no more.

* `Clever Loading` 
    - Scripts can depend on other scripts and on Assets. Define your dependency trees in clear JSON files, and Sparc will make sure everything your script needs is loaded beforehand.  
    - The loading is also optimized by loading several things in parallel, but only where there is no risk to break a dependency.

* `Powerful URL Routing` 
    - Sparc lets you define route patterns in readable JSON files.
    - Routes can be nested: a controller can redefine its own scope of routes.   
    - The URL patterns can use Regular Expressions, to extract named parameters.   
    - Methods can be specified, or not.
    - A best match strategy is used when several routes are matching the URL + User role combination.
    - Forward / Back buttons and internal links are intercepted to avoid any unwanted reload and the user is warned before exiting the application.
    - Automatic routing (link based) or direct applicative calls to the router can both be used.

* `Flexible MVC subclassing`
    - Models, views and controllers all inherit from base classes in the core, 
      then optionnaly from your own applicative level base-classes.

##  Sparc's cool plugins

* `PicoReact`
    - Some like it reactive, others not. Some like reactive only in some specific application parts...  
    PicoReact is a lightweight plugin that brings reactivity if and only where you need it !

* `Integrated websocket message bus`
    - Sparc includes as plugin a websocket webworker, and a message-bus wrapper (front-side) are acting together with a node.js websocket-Redis gateway (backend-side) to allow the application simple yet powerfull access to a front-back bus.  
    Get live events from your backend, send it live commands, or do peer-to-peer communication with other application users...

* `External error logging`
    - Javascript Warnings and Errors in the console (from your code or directly from the browser) can trigger a fire-and-forget Ajax to any external service to have them store in a database, sent formated emails, etc...
    - The error-level, the stack-trace and all relevant informations (like url, user, user-role) are included in the Json.

* `Minimizer for faster bootstrapping`
    - When the Sparc app first loads, there is usually a minimal set of components always needed for all user roles.  
    This bootstrapping can be further accelerated by having only on request for these components, for a bundled, minimized file. 
    But, as developper, you wan to keep different components isolated in different files (also to allow clean use of browser's debuging tools) and you want to avoid tedious "building" processes for front-end js files !  
    Minimizer solves this apparent contradiction by delivering to the front a last-minute minimized bundle, built on the fly from the set set of files configured.  
    As it is configured from the main configuration file, it can easily have different behaviours between dev / acceptance or production environments. (typically no minimization in dev, partial in acceptance and full optimization in prod.)

