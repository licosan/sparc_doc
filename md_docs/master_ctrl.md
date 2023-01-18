# Setting up a master controller
Often, in your SPA, some modules need to be set in place at startup, and have code managing them during the whole livetime of the SPA.
It can be for example a menu module, a user-session corner (showing user info), a notification corner, etc...  
These "modules" are not really part of the routing flow: No matter which route you came from (ie no matter what you're doing in the main-content part of the app.), these modules should always be there, and operational.
For example, imagine you arrive via a URL like "https://myapp.com/sales/figures/123". You'll probably have some controller managing sales, showing you some statistics about sales 123.
This means you need an "always-there, since the start" controller instance.
Not only