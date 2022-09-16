# Understand the routing

**Routes delegations:**

- Routes must be defined in the ***"baseRoutes.json"*** file in the the application's config.
- However, when you have a large application, and therefore many routes, managing them all in a single file can become tedious. Therefore Sparc allows for route delegation, by having controller's configuration files deined their own (sub-) routes.
    - A controller can only use URLs beginning by the URL which lead to him. (hence the "delegation" aspect much like DNS zones).  
- When analysing the Browser's URL, the router will try all the known routes at that moment (which changes with delegation when new controllers are loaded).  


**The route-findging algorithm goes as this:**
```
    1. Try to match the current URL againts each of all known routes.

    2. When the URL matches, check that the user role matches the route role too,  
       unless the route role is '*' (meaning everyone)

    3. When something doesn not match, discard the route.

    4. When both are ok, give this route a score which is the number of matching parts ,
       then keep it as candidate.

    5. When all known routes are exhausted, the candidate with the highest score is used.
```
**The routing algorithm then goes as this:**
```
    1. When the best route is found (see above), the router loads the config of 
       the corresponding controller.

    2. It then adds the routes eventually defined by that controller config. to
       the global routes list.

    3. It now retries to route the URL, to see of some of these new routes give 
       a better score. If yes, it recursively goes back to step 1.

    4. When the route does not change anymore, we have found the best possible route, 
      delegations included. It is now time to :

        1. Instantiate the controller.

        2. Choose the proper method:
            - If a method was specified in the route definition (see below), use it.
            - If not then use the last part of the url as the method name.

        3. Build a parameters object conatining :
            - Static parameters found in the route definition (see below).
            - Dynamic parameters extracted (and enventually regexp-validated) from the URL
```




Each part of the object above is described separately below :

### URL matching
`url` : **String [Mandatory]** Is the URL pattern that the router will try to match the current browser's URL against.  

- It can contain static parts, and dynamic parts (for parameters).
- For static parts (like ***"/module1/user"*** in the example above), the router will try to match it 'as is', at the right place.  
- For dynamic parts (like ***:name*** or like ***":uid(\\d+)"*** in the example above), the router will take everything until the next **'/'**, or until the end of the URL.
- The string after the **':'** will be used as parameter name.  The parameter value (transmitted to the controller's method) will be the actual string found at this place of the URL.
- If the parameter name is followed by brackets, their content will be used as a regular expression to validate the parameter. Failure to validate a parameter leads to this route not matching.

### Roles matching
`role` : **String [Mandatory]** Is the role to match with the current user role. Specifying **'*'** matches any user role.

### Controller selection
`controller` : **String [Mandatory]**  Is the controller's relative path inside the controllers basePath.  
This field is mandatory, unless the `exturl` is used instead.

### Method selection
`method` : **String [optional]** If specified: the name of the method to call in that controller.  
In absent, the last segment of the URL will be used.

### Method parameters
`params` : **String [optional]** An key:value object of static parameters passed to the method, alongside eventual dynamic parameters.  
**Caution**: if the same key is found as dynamic and static parameter, **the static value will prevail**.

### Special routing cases:

#### The default route
At least one route shall have as `url` the special value ***"!defaultroute"***  
This route is used as the last-ressort route, when no matches could be found.  
This is the equivalent of a server 404 page (But there is no server, hence no status code here)  
The other parameters are as usual, no dynamic parameters are of-course allowed.  
You could have different default pages for different roles if you want (if it make sense for you, otherwise just use one default route with *** "role" : "\*" ***)



#### External URLs
If `exturl` is used instead of `controller`, then this route is considered to be external to the application and the user will be redirected to it.  
The `role` can be used in conjuction with it, but `method` and `params` are meaningless.