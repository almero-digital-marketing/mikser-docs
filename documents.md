## Documents
Mikser does not force any predefined document structure. It is general purpose static site generator. It is up to you to  organize your documents. Documents might have a front matter and/or content. You can also have documents in a structured format. There are tree document types that are supported in Mikser.

* **Structured data formats** - Mikser supports documents in [YAML](http://www.yaml.org/), [TOML](https://github.com/toml-lang/toml), [ArchieML](http://archieml.org/), [CSON](https://github.com/bevry/cson), [JSON5](http://json5.org/) or any other format with a custom plugin
* **Markup documents with front matter** - You can have [Markdown](http://daringfireball.net/projects/markdown/) or [Textile](http://redcloth.org/textile/) documents with front matter. Front matter can be in any of the structured data formats. You can add more markup formats with plugins.
* **Anything with front matter** - You can have documents in any format (XML, HTML, etc.) with front matter.

Documents are imported into Mikser database with the following structure.

* `_id` - The relative path of the document inside documents folder
* `stamp` - System value used by Mikser to track changes inside documents folder
* `importDate`  - When the document is imported in the database
* `source` - Absolute path to the document
* `destination` - Absolute path to the rendered document inside `out` folder
* `url` - Absolute URL of the document
* `meta` - Structured data loaded from the front matter or the document itself
* `content` - Content of the document, if the document has a front matter the content will start right after, if the document is in structured data format the content will be empty
* `mtime`, `atime`, `ctime`, `birthtime` - File time information for the document
* `size` - Size of the document in bytes

### Structured data documents
Structured data is stored inside document's `meta` property. The format of the structured data is determined by the document extension. There are several reserved properties used by Mikser, all of them are optional.

* `layout` - The absolute path of the layout used to render the document. The absolute path to the layout is relative to the `layouts` folder.
* `href` - URL like string used to identify a document. It is useful in multi-language sites where you will have alternate documents with the same `href` and different languages'.
* `lang` - The value of the lang attribute identifies the language (in [ISO 639-1](http://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) format) and optionally the region (in [ISO 3166-1](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) format)
* `destination` - The destination is absolute path of the rendered document. This will overwrite the default `destination` value. The destination is relative to the `out` folder.
* `render` - Boolean value that tells Mikser if the document should be rendered. The default value is `true`.

If the structured document has a `layout`, but does not have a `destination` it will be rendered with the same same name and `.html` extension inside `out` folder. If it does not have `layout` it wont be rendered and just imported in the runtime.

#### Example
`item.yaml` [YAML](http://www.yaml.org/) document that represents an item in an on-line shop.

```yaml
layout: /shop/item.jade
href: /items/item-1
lang: en
title: Item title
description: Item description
features:
- Feature 1
- Feature 2
- Feature 3
images:
- caption: Image caption 1
  image: /path/to/the/image1.jpg
- caption: Image caption 2
  image: /path/to/the/image2.jpg
```

### Documents with front matter
The same rules that apply to the structured documents apply to the front matter. The front matter is considered to be in [YAML](http://www.yaml.org/) by default. 

#### Example
`post.md` [Markdown](http://daringfireball.net/projects/markdown/) document with [YAML](http://www.yaml.org/) front matter representing a post inside a blog.

```md
---
layout: /blog/post.ect
title: Item title
description: Item description
date: 2015-10-12
---
# Example post
This is the content of the post in [Markdown](http://daringfireball.net/projects/markdown/).
```

If you want you can use any of the structured data formats by adding it at the end of the beginning of the front matter. In this example you can see front matter in `JSON`.

```md
---json
{
	"layout": "/blog/post.ect",
	"title": "Item title",
	"description": "Item description",
	"date": "2015-10-12"
}
---
# Example post
This is the content of the post in [Markdown](http://daringfireball.net/projects/markdown/).
```

### Anything with front matter
If you want you can have documents in any format. Those documents can have front matter too. Here is an example of the previous post using HTML instead of Markdown. Mikser will keep the document extension inside `out` folder.

```html
---
layout: /blog/post.ect
title: Item title
description: Item description
date: 2015-10-12
---
<h1>Example post<h1>
<p>his is the content of the post in <a href="http://daringfireball.net/projects/markdown/">Markdown</a>.</p>
```

### Meta data routing
You can import meta data to the document based on the folder structure where documents are located. Meta data configuration is an array of routes defined inside `mikser.yml`.

```yaml
routes:
- /posts/:category/:date/*postId
- /items/:category/*itemId
```

If you store your post inside `/posts/nodejs/2015-12-10/post-1.md` it will merge some extra properties in its meta data. This is equal to adding the following information to the document front matter.

```yaml
category: nodejs
date: 2015-12-10
postId: post-1.md
```