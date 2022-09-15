# Application configuration
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
    "level":"WARNING",
    "message":"In template employeeViews/EmployeeView.html, badvariable is not defined !",
    "user":"nike",
    "url":"https://mydomain.com/employees",
    "appName":"MyApp 2.0",
    "stacktrace":[
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


### Message Bus config
This the configuration of the messagebus module. As messageBus uses Websocket-based communication, a lot of this config. has to do with WSS.

* `enabled` :  **Boolean** When false, the messageBus is ...disabled ;-)
* `pathToWorker"` : **String** This lib also uses a webworker which cannot be launched through the normal dependency system. This is the path to the worker script.  
Standard value is ***"/core/libs/MessageBusWorker.js"***.
* `protocol"` : The protocol part of the URL to the websocket server.
Standard value is ***"wss:"***.
* `host"` : The host part of the URL to the websocket server. If absent or empty, the host will be extracted from the current browser's URL (same URL is a requirement often imposed by the browser, firewalls, proxies... anyway)
* `port"` : The port part of the URL to the websocket server
* `path"` : The (Where the HTTP-Upgrade will take place)
* `connectTimeout` : ***Float*** If the server remains silent at a connection attempt, connection will timeout after this many seconds.  
**Caution** : If ***autoReconnect*** is > 0, the  Auto-reconnect will occur after ***connectTimeout + autoReconnect*** seconds, unless the door is immediately slammed in your face right away (then ***autoReconnect*** seconds). If your WSS server is hidden behind an Nginx (which you should really do in prod.), you should also pay attention to the Nginx parameters ***proxy_connect_timeout, proxy_send_timeout and proxy_read_timeout*** 
* `autoReconnect` : ***Float*** If zero (or false): no auto-reconnect upon connection loss. Otherwise, the time, in seconds (decimals ok), before he FIRST reconnecting attempt. (ex: 5 seconds)
* `autoReconnectTimeFactor` : ***Float*** : on every successive reconnection failure, the auto-reconnect time is multiplied by this factor, to avoid reconnect-spamming when the WSS server is down. 
* `autoReconnectTimeMax` : ***Float*** : The ceiling value for the auto-reconnect time. 
For example with ***autoReconnect:5, autoReconnectTimeFactor:2 , autoReconnectTimeMax:60***, attempts would occur at ***(connection loss) + 5sec, + 10sec, +20sec, +40sec then every minute***)
* `autoReconnectJitterPercent` : ***Float*** : If your websocket server goes down, all clients are loosing connection precisely at the same time. Therefore, their reconnection attempts will be synchronized, which is not what you want when you'll bring your WSS server back up !  
If this parameter is non-zero, it represents the percentage of random "jitter" applied on the next reconnect time. For example: with a reconnect time of 30 sec, and ***autoReconnectJitterPercent:10 *** : the actual reconnection will occur between 28.5 and 31.5 seconds later.


```json
    "messageBus":{
        "enabled": true,
        "pathToWorker": "/core/libs/MessageBusWorker.js",
        "protocol": "wss://",
        "port": "",
        "path": "/msgbus",
        "connectTimeout": 5,
        "autoReconnect": 5,
        "autoReconnectTimeFactor": 1.3,
        "autoReconnectTimeMax": 30,
        "autoReconnectJitterPercent": 10
    }
```

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

See ["Understanding the routing"](./routing.html) section for more details.
