# References

Within this documentation, and within Ractive's code, a *reference* is a string that refers to a piece of data - in other words, within a `{{name}}` [mustache](mustaches.md), `name` is the reference.

By themselves, references are useless - they must *resolve* to a *[keypath](keypaths.md)* before we can do anything with them (like render their value). If the reference exists with a section mustache, it may need to be resolved *in the context of that section*. In fact, because sections can be nested, we have to resolve each reference within its *context stack*.

The resolution algorithm looks like this:

1. If the context stack is empty, skip to (6).
2. Find the innermost context on the current context stack.
3. See if the keypath which is `context + '.' + reference` points to a value
4. If so, resolve with that keypath
5. Otherwise, remove the innermost context from the stack. Repeat (1-5).
6. See if `reference` is a valid keypath by itself. If so, resolve.
7. If we couldn't resolve the reference, add it to the 'pending resolution' pile. Each time the data changes, repeat steps (1-7).


## Huh?

That's a little bit abstract. The following example may help explain:

```html
{{#user}}
  <p>Welcome back, {{name}}!
    {{#messages}}
      You have {{unread}} unread of {{total}} total messages.
      You last logged in on {{lastLogin}}.
    {{/messages}}
  </p>
{{/user}}
```

```js
ractive = new Ractive({
  el: container,
  template: myTemplate,
  data: {
    user: {
      name: 'Jim',
      messages: {
        total: 10,
        unread: 3
      },
      lastLogin: 'Wednesday'
    }
  }
});
```

We start with an empty context stack, so to resolve the `user` in `{{#user}}` we skip ahead to step 6 of the algorithm. Is `user` a valid keypath - i.e. does `ractive.data` have a `user` property? Why yes, it does.

Within the `{{#user}}` section, `user` is a context. So we now have a non-empty context stack - `['user']`. So when we come to resolve the `name` in `{{name}}`, we go to step 2. The innermost (and only) context is `user`, so we test the keypath `user.name`. Is it valid? Why yes, it is. So the `name` resolves to `user.name`.

Next up, `{{#messages}}`. The innermost context is still `user`, so we test `user.messages` - bingo. Because it's a section mustache, anything inside it now has a two-level context stack (`['user', 'user.messages']`).

We take `{{unread}}` and `{{total}}` and apply the same algorithm - sure enough, they resolve to `user.messages.unread` and `user.messages.total`.

What about `{{lastLogin}}`? Again, we take the innermost context from the stack - `user.messages`. Is `user.messages.lastLogin` a valid keypath? No, it isn't. So we take the next innermost context - `user`. As it turns out, `user.lastLogin` is a valid keypath, so the reference resolves.

Most of the time you don't need to be aware that this is going on, especially if you're already familiar with [Mustache](mustaches.md), but it's useful to have a background understanding.


## Lists

This is also how list sections work. Consider the following:

```html
{{#items}}
  {{content}}
{{/items}}
```

```js
ractive = new Ractive({
  el: container,
  template: myTemplate,
  data: {
    items: [{ content: 'zero' }, { content: 'one' }, { content: 'two' }]
  }
});
```

The contents of the `{{#items}}` section are rendered once for each member of `items`. Each time round, the context changes - the first time it is `items.0`, then it is `items.1`, then it is `items.2`.

With a context of `items.0`, the `content` reference resolves to `items.0.content`, and so on.

## Current context - `{{.}}` or `{{this}}`

Using `{{.}}` or `{{this}}` (they refer to the same thing) resolves to the current data context.

For example, sometimes you will have lists of primitives (i.e. strings and numbers) rather than objects. To refer to the primitives, we must use the *implicit iterator*:

```html
{{#items}}
  {{.}}
{{/items}}
```

```js
ractive = new Ractive({
  el: container,
  template: myTemplate,
  data: {
    items: [ 'zero', 'one', 'two' ]
  }
});
```

Whenever Ractive sees `{{.}}`, it simply resolves it to the innermost context - `items.0`, `items.1`, `items.2`.

In Ractive (but not Mustache), you can use `{{this}}` in place of `{{.}}`. This is because in expressions - `{{this.toFixed(1)}}` looks much nicer than `{{..toFixed(1)}}` (for example).

## Restricted references

Sometimes you may want to reference a property within the current context regardless of whether that property currently exists. For those situations you can use what's called a *restricted reference* (note that this feature does not exist in vanilla Mustache):

```html
{{#options}}
  <label><input type='checkbox' checked='{{.selected}}'> {{description}}</label>
{{/options}}
```

This feature is particularly useful with [two-way binding](two-way binding.md) as these references have to be resolved immediately.

You can also use the form `{{./selected}}` if you prefer, or `{{this.selected}}`, both of which are the same as `{{.selected}}`.


### Ancestor references

Very occasionally, you might need to refer explicitly to a property higher up in the tree to avoid naming conflicts. You can do that by prefixing references with `../` (or `../../`, or `../../../`...):

```html
<h1>Blog posts by {{name}}</p>

<ul class='blog-posts'>
  {{#posts}}
    <li><a href='{{ slugify(../../name) }}/{{ slugify(name) }}'>{{name}}</a></li>
  {{/posts}}
</ul>
```

```js
var ractive = new Ractive({
  el: document.body,
  template: myTemplate,
  data: {
    name: 'Rich',
    posts: [
      { name: 'This is a blog post' },
      { name: 'And so is this' }
    ],
    slugify: function ( str ) {
      var slug;
      /* SOME CODE HAPPENS */
      return slug;
    }
  }
});
```

In this example, some damn fool decided it would be a good idea to give posts a 'name' property as well as authors. Which means that in the `href` attribute, it wouldn't be possible to refer to the root-level `name` property, because it would always resolve to the `name` property of the current post - and that's no good, because the root-level `name` property is used to construct the URL.

By using `../../name` instead of `name`, we're saying 'go up one level (to `posts`), then up one more (to the root), and *then* look for the `name` property'.

### Root references

You can also navigate directly from the top of the data hierarchy with `~/`. Note that this is the root of the ractive instance in which the template appears, not the entire view hierarchy of nested components.

The above example could also use a root reference:

```html
<h1>Blog posts by {{name}}</p>

<ul class='blog-posts'>
  {{#posts}}
    <li><a href='{{ ~/name }}/{{ name }}'>{{name}}</a></li>
  {{/posts}}
</ul>
```

