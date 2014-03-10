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

Here are a few ways in which the `module` tag makes for a better
programming model:

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

## External module scripts

As a replacement for `script`, of course `module` also needs to allow
loading module sources from external files:

```html
<module src="main.js"></module>
```

## Named modules

Bootstrapping code sometimes needs to be able to ensure that its core
functionality will be immediately available without loading any
external dependencies. For this reason, it's also possible to define
named modules inline:

```html
<module name="adder">
export default function(x, y) {
  return x + y;
}
</module>

<module>
import add from "adder";
console.log(add(1.1, 2.2)); // 3.3ish
</module>
```

This is also convenient for writing small test cases, examples, etc.

## So, is this a packaging format?

No. ...

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
