# The `module` tag explained

ES6 modules make it possible to state module dependencies
declaratively and fetch them asynchronously, without boilerplate
asynchronous loading logic. On the HTML side, there has to be a way to
kick off the asynchronous loading of an application's modules. This
could be done programmatically:

```html
<script>
System.import("myapp").then(function(app) {
  // ...
});
</script>
```

But the `module` tag provides a way to eliminate the boilerplate at
app top-level, too:

```html
<module>
import app from "myapp";
...
</module>
```

In fact, the `module` tag provides an overall programming experience
that is similar to `script` but improves it in a few key ways.


## `module` is a better `script`

...


## The "mixed model" of app data

It's common for web apps to have uncached HTML source that loads
uncacheable data by mixing it inline with source code. This is just as
convenient with the `module` tag, but it's now also interoperable with
(possible highly cacheable) external source files. For example, an app
might have most of its application logic in cacheable modules, but its
startup logic embedded in HTML with uncacheable data baked in:

```html
<!doctype html>
<html>
  <head>
    <module>
      import app from "myapp";
      app.preload({
        "site-data": [
          // lots of preloaded data ...
        ]
      });
    </module>
    <link rel="stylesheet" href="base.css">
  </head>
  <body>
    <!-- ... -->
  </body>
</html>

```

One reason this pattern is popular is that it's so convenient to code
in templated frameworks like PHP or Rails:

```php
app.preload(<%= escape_unicode(json) %>);
```

Of course, it's *possible* to do the same thing with blocking `script`
tags or XHR and async logic, but the goal of `module` is to subsume
the conveniences of `script` so programmers can comfortably move to an
all-modules development model without loss of ergonomics, especially
for popular idioms.

## So, is this a packaging format?

No. ...
