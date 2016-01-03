## Layouts
In Mikser layouts define how documents will be rendered. Layouts are stored in the `layouts` folder. Mikser has build-in support for the following layout(template) engines - [Jade](http://jade-lang.com/), [Eco](https://github.com/sstephenson/eco), [Ect](http://ectjs.com/), [Ejs](http://ejs.co/), [Swig](http://paularmstrong.github.io/swig/), [Nunjucks](http://mozilla.github.io/nunjucks/) and [Twig](http://twig.sensiolabs.org/).

Mikser selects the right template engine to use based on the layout extension. Layouts can have front matter. The data from the front matter is is imported in the layout's meta property. There are several reserved properties and all of them are optional.

* `layout` - Each layout can define a parent layout. This is the absolute path to the parent layout. The absolute path is relative to the `layouts` folder.
* `data` - This property is used to define data queries used by the layout to retrieve data from the database.
* `blocks` - A list of layouts which will imported as blocks(partials) and used inside the layout.
* `shrotcodes` - A list of layouts which will be exposed as short codes that can be used inside the documents which use this layout.
* `plugins` - A list of plug-ins used in the layout.

Mikser supports different template engines working together, It's perfectly fine to use [Ect](http://ectjs.com/) for a layout, [Ejs](http://ejs.co/) for its parent layout, [Jade](http://jade-lang.com/) for the blocks and [Swig](http://paularmstrong.github.io/swig/) for the short codes.

### Basic layout usage
Let's define a very basic layout that will render a [Markdown](http://daringfireball.net/projects/markdown/) document as an HTML page and use [Ect](http://ectjs.com/) template engine. Let's call this layout `html5.ect` and save it directly in layouts folder.

```html
<!DOCTYPE html>
<html>
  <head>
  </head>
  <body>
    <%- @content %>
  </body>
</html>
```

This layout will render a document's content inside a `body` tag. The `@content` holds a rendered document contents.

```md
---
layout: /html5.ect
title: Post title
description: Post description
date: 2015-10-12
image: /images/image-1.jpg
---
# Example post
This is the content of the post in [Markdown](http://daringfireball.net/projects/markdown/).
```

For our [Markdown](http://daringfireball.net/projects/markdown/) document the `@content` will output the rendered HTML of the document's content.

### Referring meta data
There are several objects that you can access inside a layout.

* `document` - This object holds currently rendered document. You can access document meta data by doing `document.meta`.
* `layout` - This is an on object pointing to the current layout. You can access layout meta data by doing `layout.meta`.
* `data` - This object holds all the data loaded by the queries defined in the layout's front matter `data` element.
* `blocks` - This object has functions that will render the blocks defined in the layout's front matter `blocks` element.

Let's extend our previous example by using the document `title` and `description` in the `html5.ect` layout.

```html
<!DOCTYPE html>
<html>
  <head>
    <title><%= @document.meta.title %></title>
    <meta name="description" content="<%= @document.meta.description %>" >
  </head>
  <body>
  <%- @content %>
  </body>
</html>
```

### Layout inheritance
In Mikser layouts support inheritance. We already defined a very generic layout. Let's create another one that will be used just for posts. This time we will use [Jade](http://jade-lang.com/) as a template engine.

```jade
---
layout: /html5.ect
---
article.post
  img(src= document.meta.image alt= document.meta.title)
  span= document.meta.date
  div!= content
```

Now we have to change the post document so it can use the new layout.

```md
---
layout: /post.jade
title: Post title
description: Post description
date: 2015-10-12
image: /images/image-1.jpg
---
# Example post
This is the content of the post in [Markdown](http://daringfireball.net/projects/markdown/).
```
Let's see how the inheritance works. Fist Mikser renders the [Markdown](http://daringfireball.net/projects/markdown/) document content. Then it renders the layout defined in the document meta data. In our example this is `/post.jade`. As you can see we are using the document meta data and the content inside this layout. Then the result of this render is passed to the layout defined in the `/post.jade` in our case this is `/html5.ect`. Now when we refer to the `content` inside the `/html5.ect` it will output the result of the rendered `/post.jade`.

Lets create another document this time an [YAML](http://www.yaml.org/) document representing an item in an on-line shop and create a layout for this kind of documents using a [Swig](http://paularmstrong.github.io/swig/) template engine.

```yaml
layout: /shop/item.swig
title: Item title
description: Item description
features:
- Feature 1
- Feature 2
- Feature 3
```

Now lets create the `/shop/item.swig` layout, it uses `/html5.ect` as a parent layout. We can group layouts in folders and we will put this one in a folder called `shop` inside `layouts` folder.

```html
---
layout: /html5.ect
---
<h1>{{ document.meta.title }}</h1>
<p>{{ document.meta.description }}</p>
<ul>
{% for feature in features %}
  <li>
    {{ feature }}
  </li>
{% endfor %}
</ul>
```

As you can see when the document has no content we can work only with the meta data. This will render first the `/shop/item.swig` layout using meta data from the document and then `/html5.ect` layout which will get the result of the previous render in the `content` property.

### Using data queries
In the layout front matter we can define queries which will return collections of documents based on a search criteria. Later on we can iterate over this collections in the layouts.

#### Layout queries
Let's create another layout that will list all the items in the shop. We will put this layout in `/shop/items.swig`. 

```html
---
layout: /html5.ect
data:
  items: /shop/item.swig
  orderedItems:
    layout: /shop/item.swig
    orderBy: meta.title
  reversedItems:
    layout: /shop/item.swig
    orderBy: 
      field: meta.title
      order: -1
---
<ul>
{% for item in data.items %}
  <li>
    {{ item.meta.title }}
  </li>
{% endfor %}
</ul>
```

We have to create a document that will use this layout as well. We will call the document `items.yml`.

```yaml
layout: /shop/items.nunjucks
title: List of all items
description: This will render a listing
```

In the layout you can define as many data queries as you like. This is the simplest syntax, you have to provide only the layout of the documents to select. You can also specify sort order in the `data` definition. As you can see you can a shorter syntax for the sorting too passing only the document property to use for the sorting. If you need you can also provide the sort order 1 for ascending -1 for descending.

#### Context queries
It's very convenient to query for documents by the layout but sometimes this is not enough. Mikser layouts supports external queries that has access to the all the properties that are available in the layout.


```js
{
  $and: [ 
    { 'meta.layout': /shop/item.swig
    { 'meta.features': { $in: layout.meta.features }}
  ]
}
```

You can save this query as `items-by-feature.query` in a `data` folder inside your project. Then you can refer it in the layout.

```html
---
layout: /html5.ect
features:
- Feature 1
- Feature 2
data:
  itemsByFeature:
    query: /data/items-by-feature.query
    orderBy: meta.title
---
<ul>
{% for item in data.itemsByFeature %}
  <li>
    {{ item.meta.title }}
  </li>
{% endfor %}
</ul>
```
As you can see you have an access to all of the meta data inside the query. In this example you will load all documents that has `Feature 1` or `Feature 1` which are defined in the layout's meta data. You can use any JavaScript expression in the queries. Queries use [MongoDB query syntax](https://docs.mongodb.org/v3.0/reference/operator/query/).

### Referring other documents from layouts
One of the very core things in writing a layout is refereeing to other documents or assets. You can always refer to a document by using `document.url` or the absolute URL of an asset `/images/image.jpg`. The problem with this approach is that you won't be able to open you generated web site from the file system. And it should be placed in the domain root when uploaded to a hosting provider. It's much better to use relative URL's. Mikser defines a helper method `href` that gives you a relative URL.

```html
---
layout: /html5.ect
data:
  posts: /post.jade
---
<ul>
{% for post in data.posts %}
  <li>
    <a href="{{ href(post) }}">{{ item.meta.title }}</a>
    <img src="{{ href(post.meta.image) }}">
  </li>
{% endfor %}
</ul>
```

`href` returns a relative path to any given absolute URL of an asset or a document. It won't check if the URL  exists. It calculates the relative path by the given input only and the result is relative to the currently rendering document. There is another interesting concept to know about, you can define a `href` inside the document meta data.

```yaml
layout: /html5.ect
href: /services
title: Services
```

Having `href` inside a document will let you refer to the document by this `href` no matter what file name you use to save it. Lets update the `/html5.ect` and add a link to the document with `href` equal to `/services`.

```html
<!DOCTYPE html>
<html>
  <head>
  </head>
  <body>
    <a href="<%- @href('/services') %>"><%= @href('/services').meta.title %></a>
    <%- @content %>
  </body>
</html>
```

This will use the relative path to the `/services` in all documents that use `/html5.ect` as a layout. Later on if you change the name or move the `/services` document inside different folder all the links will update accordingly.

Another important aspect of the `href` method is that it keeps track of the cross document references and when you change the `/services` title, Mikser will regenerate all the document that refer to it with the new title. We highly recommend using `href` as much as possible.

If you don't specify a `href` in the document's meta data it will be initialized with the absolute path to the document relative to the `documents` folder. If you save the service document to `services.md` you can use `/services.md` as a `href` argument.

```html
<a href="<%- @href('/services.md') %>"><%= @href('/services.md').meta.title %></a>
```

If you want to get a relative path to the root you can user `href('/')` to get it or pass get relative URL to a style sheet by `href('/styles/style.css')`.

### Blocks(Partials)
You can use any layout as block(partial) in any other layout. In Mikser we use the term blocks and you have to define them in the layout meta data. Let's create a layout that will render a navigation for our site. We will call it `/navigation.ect`.

```html
<a href="<%- @href('/services.md') %>"><%= @href('/services.md').meta.title %></a>
<a href="<%- @href('/contacts.md') %>"><%= @href('/contacts.md').meta.title %></a>
```

#### Using plain blocks
Now lets use this layout as a block inside `/html5.ect` so every page will have a menu.

```html
---
blocks:
  navigation: /navigation.ect
---
<!DOCTYPE html>
<html>
  <head>
  </head>
  <body>
    <%- @blocks.navigation() %>
    <%- @content %>
  </body>
</html>
```

Everything that applies to the layouts applies to the blocks as well. All blocks defined in the `/html5.ect` layout will be available to the child layouts using it. If some of them override it with another block it will be used in `/html5.ect` instead of the `/navigation.ect`.

#### Using blocks with options
You can pass any object to a block and refer it inside the block as `options`. Here is a block `/post-preview.nunjucks` that will render a preview of a post.

```html
<a href="{{ href(post) }}">{{ options.meta.title }}</a>
<img src="{{ href(options.meta.image) }}">
```

Now we will update `/posts.nunjuncks` so it will use this block.

```html
---
layout: /html5.ect
blocks:
  postPreview: /post-preview.nunjucks
data:
  posts: /post.jade
---
<ul>
{% for post in data.posts %}
  <li>
    {{ blocks.postPreview(post) }}
  </li>
{% endfor %}
</ul>
```

### Short codes
Short codes are very similar to the blocks. The main difference is that they can be used inside a document. A document can use only the short codes defined in its layout.

```md
---
layout: /post.jade
title: Post title
description: Post description
date: 2015-10-12
image: /images/image-1.jpg
---
# Example post
This is the content of the post in [Markdown](http://daringfireball.net/projects/markdown/).
[video src="http://youtube.com/video.mp4" title="Video title"]
Video Description
* You can use the document syntax inside a short code
* You can even nest short codes

[audio src="http://soundcloudcom/audio.mp3"]
[/video]
```

Inside the layout for the short code you will have an access to the attributes inside an `options` object and to the content of the short code with the `content`. Let's create `/video.jade`.

```jade
div.video
  h1= options.title
  iframe(src= options.src)
  div!= content
```

Now we have to define the short code in the layout. Everything that applies to the layouts applies to the short codes as well.

 ```jade
---
layout: /html5.ect
shortcodes:
  video: /video.jade
  audio: /audio.jade
---
article.post
  img(src= document.meta.image alt= document.meta.title)
  span= document.meta.date
  div!= content
```

This will render the short codes used in the document and all of this will go to the content in the main layout.

### Paging
Layouts also support paging. You can define paging in any layout by adding two properties to the layout meta data.

```html
---
layout: /html5.ect
blocks:
  postPreview: /post-preview.nunjucks
data:
  posts: /post.jade
pageBy: posts
pageSize: 3
---
<ul>
{% for post in paging.data %}
  <li>
    {{ blocks.postPreview(post) }}
  </li>
{% endfor %}
</ul>
{% if paging.next %}
<a href="{% href(paging.next) %}">Next</a>
<span>or</span>
<a href="{% href(paging.page(paging.current + 1)) %}">Next</a>
{% endif %}
```

There is a special object that holds paging information and it is available in the layout when you define `paging` in the layout meta data. The `paging` object has several properties and some helper methods.

* `next` - This property gives you a `href` to the next page or `undefined` if there is no next page. 
* `prev` - This property gives you a `href` to the previous page or `undefined` if this is the first page being rendered. 
* `data` - This property gives you a portion of the data for the page being rendered. 
* `current` -  This is zero based index of the page being rendered.
* `pages` - This is the total number of pages that will be rendered.
* `page(pageNumber)` - This method returns a `href` to the page by a give number.

When you define a paging in your layout it will render several web pages adding a page number just before the extension. If we assume that there will be 3 pages the output of the rendering will be separated into 3 files - `posts.html`, `posts.1.html`, `posts.2.html`.

### Using auto layouts
Sometimes its not appropriate to add meta data to a document, but you still have to define a layout for those documents. You can define a layout to a set of documents in the Mikser configuration file.

```yaml
layouts:
- pattern: '/posts/**'
  layout: /post.jade
- pattern: '/items/**'
  layout: /shop/item.swig
```

This will set the layout for all documents inside `/posts` folder to `/post.jade` and `/shop/items.swig` to all documents inside `/items` folder.