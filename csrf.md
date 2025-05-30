# [CSRF PoC Tool](https://csrfshark.github.io/app/)

## Interesting Techniques
### [CSRF In Cookies And Form Parameters](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-duplicated-in-cookie)
When a CSRF token is sent with a request, as a cookie and as a POST request parameter, check if you can set arbitrary cookies.
In the Portswigger lab linked as an example, we can set arbitrary cookies using the search functionality with the following:

`https://lab-url.net/?search=test%0d%0aSet-Cookie:+csrf=fake-csrf;+SameSite=None`

The arbitrary cookie gets reflected in the response of the search request. Then, in the CSRF PoC instead of adding a `<script>` tag to auto-submit the form we can add the following:

`<img src="https://lab-url.net/?search=test%0d%0aSet-Cookie:+csrf=fake-csrf;+SameSite=None" onerror="document.forms[0].submit()" />`

This will inject the cookie into the victim's browser and auto-submit the form at the same time.

### Remove Referrer Header Dependency
If the CSRF PoC does not work because of the *Referer* header, we can tell the browser to ignore the *Referer* value and not send the header as part of the request by adding the following tag to the PoC:

`<meta name="referrer" content="no-referrer">`

*Notice the difference in the spelling of the header which is written *Referer* with one *R* and the tag name *Referrer* with two *R*s
