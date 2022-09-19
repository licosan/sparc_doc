# 2. Application configuration
There are two Json files located in /app/config :  
***appConfig.json*** : General configuration parameters for this application.  
***baseRoutes.json*** : Defines the top-level routes

## General configuration
This configuration Json file contains one separated block for every configurable core library.

### Logger config (key: "logger")
* `enabled` : **Boolean** When false, the logger is ...disabled ;-) Importantly, that means that the browser console remains the original one.
* `levels`  : **Array** Containing either 'warn', 'error' or both.
* `postUrl` : **String** The Url where the Json of the error will be posted

Example config block:
```json
    "logger": {
        "enabled": true,
        "levels": ["warn", "err"],
        "postUrl": "https://mydomain.com/log2mail.php"
    },
```

Example Error posted:
```json
    "level": "WARNING",
    "message": "In template employeeViews/EmployeeView.html, badvariable is not defined !",
    "user": "nike",
    "url": "https://mydomain.com/employees",
    "appName": "MyApp 2.0",
    "stacktrace": [
        ["processTemplate","Assets.js",["221","25"]],
        ["replaceByHtml","Assets.js",["241","66"]],
        ["loadHtml","Assets.js",["182","56"]],
        ["replaceByHtml","Assets.js",["246","14"]],
        ["...all the way up to Sparc-core..."]
    ]
```  
  
  
### Assets manager config
* `basePath` : **String** The path where all assets are stored

Example config block:
```json
    "assets": {
        "basePath": "/app/assets/"
    },
```

### Router config
* `getRoleFrom` : **String** This is a String that represents a **function or method**.  
It Will be called by the router to get the role used in routing.  
Standard value is ***"app.User.getRole"***, which uses the (early loaded) User class that you can override (see "Custom User class" below)  
**Caution** : This is needed early in the routing process, and therefore should be able to answer right away (no promise).  
If this string does not represent a callable, it is used as a plain string value. (useful for no-login-dev-mode)
* `controllersPath` : *String** The root path of controllers.  
Standard value is ***"/app/controllers"***.
* `modelsPath` : *String**  The root path of models.  
Standard value is ***"/app/models"***.
* `viewsPath` : *String**  The root path of views.  
Standard value is ***"/app/views"***.

Example config block:
```json
    "router": {
        "getRoleFrom":  "app.User.getRole",
        "controllersPath": "/app/controllers/",
        "modelsPath": "/app/models/",
        "viewsPath": "/app/views/"
    },
```


### Custom User class
**String** This is the name (without .js) of the file containing your override of the User class.  
 **Caution** : The file cannot be called "User", as the loader would consider this class is already loaded. (the Baseclass is indeed)  
Look at the base class at ***/core/baseClasses/User.js*** to see the interface.
Here is a typical example :
```javascript
'use strict'
app.LoadedClasses.User = class extends app.LoadedClasses.User {

    constructor(){
        super();
    }

    checkAuthenticated(callBack){ // Call an service to know if we are logged in
        fetch('https://mydomain/checkAuthenticated?'+crypto.randomUUID(),{
            method: 'GET',
            credentials: 'include'
            })
            .then(response => response.json())
            .then(resp => { 
                if(resp.success){
                    this.isAuthenticated = resp.data.isAuthenticated;
                    if(resp.data.isAuthenticated) { // Meanwhile get user infos
                        this.userInfo = resp.data.userInfo;
                        callBack();
                    } else {
                        console.warn('User was not authenticated !');
                        this.authUrl = resp.data.authUrl;
                        callBack(); // sparCore defaults is call gotoLogin below
                    }
                } else {
                    console.error('Server error calling checkAuthenticated !?')
                }
            });         
    }

    gotoLogin(){ // Redirect, or call a service, or show a login button... 
        document.location.href = 'https://mydomain.com/login.php';
    }
}
```


### Plugins config
For each of the available plugins, there is usually a a configuration block of the same name.
Normally, plugins should have a `enabled` flag to enable them.




## Top level routes definition
Here are the definitions of the top-level routes used by the router.
This file contains a Json  Array of route objects such as for example :

```json
    {
        "url": "/module1/user:uid(\\d+)",
        "role": "theboss",
        "controller" : "/common/User/UserCtrl",
        "method": "userMod"
    },  
```
- If you have tons of routes and want to keep this file reasonable, you can delegate "sub-routes" at each controller level.

See ["Understanding the routing"](./routing.md) section for more details.
