# The `module` tag explained

ES6 modules make it possible to express module dependencies
declaratively and fetch them asynchronously, without boilerplate
asynchronous loading logic. Now, there must be a way in HTML to kick
off the asynchronous loading of an application's modules. This could
be done programmatically:

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

Here are a few ways in which the `module` tag makes for a better
programming model:

* **Declarative imports.** Top-level code can import from external
    modules.

* **Async.** Unlike `script`, which makes you opt into async loading
    (and at least currently doesn't allow inline source with `async`
    or `defer`), the `module` tag always loads and executes
    asynchronously.

* **Everything everywhere is in a module.** There's no need to write
    global code ever, so the programming model is uniform.

* **Top-level code is in a private scope.** Unlike `script`, top-level
    variable bindings are local to the module defined in the current
    `module` tag. In particular this means no more defensive IIFEs at
    top level.

* **Strict by default.** Unlike global code, modules are automatically
    in strict mode, which cleans up several warts in JavaScript (like
    `with`). It also fixes some interoperability nightmares of
    non-strict code, in particular the semantics of block-local
    function declarations.


## The "mixed model" of app data

It's common for web apps to have uncached HTML source that loads
uncacheable data by mixing it inline with source code. This is just as
convenient with the `module` tag, but it's now also interoperable with
(possibly highly cacheable) external modules. For example, an app
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

## External module scripts

As a replacement for `script`, of course `module` also needs to allow
loading module sources from external files:

```html
<module src="main.js"></module>
```


## Shouldn't devs be putting their source code in separate JS source files?

In general, yes. But `module` is simply a better `script`, and
`script` continues to have plenty of usage, such as the "mixed model"
described above.


## Is this intended to be used for packaging?

No. The TAG is currently working on a more general and high priority
[asset packaging](https://github.com/w3ctag/packaging-on-the-web)
solution (note that there's more work to do there -- I don't think the
TAG documents are currently addressing all the important
requirements). The driving purpose of `module` is top-level or
bootstrapping code.


## What about concatenation?

Concatenation is a packaging solution, but a limited one (see above).

That said, the
[ServiceWorker](https://github.com/slightlyoff/ServiceWorker/blob/master/explainer.md)
and [ES6 Loader](https://gist.github.com/dherman/7568080) API's will
allow custom code packaging formats and concatenation. But ultimately
the better solution is enabling bundling of arbitrary assets (not just
code) via URL's and extensible package formats.


## If packages become a browser format, how can they be shared with the server?

ES6 modules separate the concept of module naming from deployment
format through the [Loader
API](https://gist.github.com/dherman/7568080). For example, you can
write
```javascript
import _ from "underscore";
```
in your module source, and configure each host environment differently
to find `"underscore"` in the appropriate location, without changing
the source code.

In fact, a packaging format can be reused in multiple host
environments, since loaders can easily be configured (or even designed
to work out of the box) with the browser packaging format.


## What about XSS filters?

The only safe way to do XSS filtering is whitelisting. For
whitelisting filters, the addition of a new HTML tag poses no risks,
because the tag is disallowed by default.

Blacklisting filters are sadly prevalent -- sadly, because they're so
easy to break. Spend any amount of time with a blacklist filter and
you'll find ways to break it. In practice, blacklist filters that
aren't being broken into aren't being broken into because no one has
tried.

In fact, the web platform adds new sources of executable script all
the time in the form of attributes (such as media events like
`onloadstart` or `onplaying`). In fact, a common way to defeat many
blacklist filters is to hide the "javascript:" pseudo URL scheme with
HTML entity encodings, which doesn't even require finding new
attributes.

In short, `module` is very unlikely to materially affect the relative
security of any (already dangerously insecure) blacklist filters.


## How do we get there from here?

The `module` tag will need to be implicitly CDATA (so code like `x <
5` doesn't cause parsing issues), automatically styled not to display
its source, and legal in the `head` of an HTML document. For all these
reasons, the stopgap solution that can be used compatibly with legacy
browsers is an equivalent `script` MIME type:

```html
<script type="application/javascript+module">
...
</script>
```

This is fully backwards-compatible and enables polyfilling in legacy
browsers. The semantics is identical to `module`. But in the longer
run, once enough browsers support `module`, that will become suitable
for production apps and will be the more ergonomic syntax.
