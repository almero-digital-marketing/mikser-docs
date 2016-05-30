## Localization
Mikser has build-in localization support with the following features

* Documents form different languages with different names for perfect SEO
* Easy to implement resource translation
* Different document structure and content for different languages
* Easy to add another localization later with almost no change in the layouts
* Add different languages in a sub-folder or in a different domain

### Multi-language web sites
You can add multi-language support in Mikser by adding an extra property in your documents meta.

```yaml
layout: /shop/item.jade
href: /items/item-1
title: Item title
description: Item description
lang: en
```

Here is the same document in different language.

```yaml
layout: /shop/item.jade
href: /items/item-1
title: Item title in German
description: Item description in German
lang: de
```

Mikser will group internally all documents by their language and when you refer a document from layout by href it will return the document that has the same language as the one being rendered.

You can name your documents and folders the way you like as long the 'href' is the same in different languages Mikser will work just fine.

If need to translate some strings in the layouts you can always create a `translation.yml` document for each language and refer to it inside the layout by `href`.

Here is translation document in English

```yaml
href: /transaltion
lang: en
submit: Submit
cancel: Cancel
```

And the same file in German. You can organize different language files the way you like.

```yaml
href: /transaltion
lang: de
submit: Einreichen
cancel: Annullieren
```

Inside a layout you can access the translated strings very easy. In the example we use `eco` syntax.

```html
<a href="#submit"><%= @href('/translation').meta.submit %></a>
<a href="#cancel"><%= @href('/translation').meta.cancel %></a>
```

### Referring documents from different languages
Sometimes you need to refer to a document with specific language inside a layout and it takes just an extra param when you call `href` to do so.

```html
<a href="#submit"><%= @href('/translation','en').meta.submit %></a>
<a href="#submit"><%= @href('/translation','de').meta.submit %></a>
```

### Alternates
If you have multiple language versions of a URL, each language page must identify all language versions, including itself. You can read more about this concept [here](https://support.google.com/webmasters/answer/189077). In Mikser there is a helper method `hrefLang` that will give you a list of all documents with the same `href` and different `lang`.

```html
<!DOCTYPE html>
<head>
    <% for lang, alternate of @hrefLang() : %>
    <link href="http:/<%- alternate.url %>" rel="alternate" hreflang="<%- lang %>">
    <% end %>
</head>
<body>
<%- @content %>
</body>
```

In this example we assume that the languages are organized in different domains. If they are under the same domain we can remove the leading `http:/`.

[![Analytics](https://ga-beacon.appspot.com/UA-78544431-1/localization.md?pixel)](https://github.com/igrigorik/ga-beacon)