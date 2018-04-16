[github okhttp wiki](https://github.com/square/okhttp/wiki/Recipes)

### Accessing Headers
> Typically HTTP headers work like a Map<String, String>: each field has one value or none. But some headers permit multiple values, like Guava's Multimap. For example, it's legal and common for an HTTP response to supply multiple Vary headers. OkHttp's APIs attempt to make both cases comfortable.
 
> When writing request headers, use header(name, value) to set the only occurrence of name to value. If there are existing values, they will be removed before the new value is added. Use addHeader(name, value) to add a header without removing the headers already present.
 
> When reading response a header, use header(name) to return the last occurrence of the named value. Usually this is also the only occurrence! If no value is present, header(name) will return null. To read all of a field's values as a list, use headers(name).