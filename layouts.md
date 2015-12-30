## Layouts
In Mikser layouts define how documents will be rendered. Layouts are stored in the `layouts` folder. Mikser has build-in support for the following layout(template) engines - [Jade](http://jade-lang.com/), [Eco](https://github.com/sstephenson/eco), [Ect](http://ectjs.com/), [Ejs](http://ejs.co/) and [Swig](http://paularmstrong.github.io/swig/).

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

For our [Markdown](http://daringfireball.net/projects/markdown/) document the `@content` will output the rendered HTML of the document`s [Markdown](http://daringfireball.net/projects/markdown/) contents.

### Referring meta data
There several objects that you can access from the layout.
* `document` - This object holds currently rendered document. You can access document meta data by doing 'document.meta'.
* `layout` - This is an on object pointing to the current layout. You can access layout meta data by doing 'layout.meta'.
* `data` - This an object that holds all the data loaded by the queries defined in the layout's front matter.
* `blocks` - This object has functions that will render the blocks defined in the layout's front matter.

Let's extend our previous example by using the document title and description in the `html5.ect` layout.

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
In Mikser layouts support inheritance. We already defined a very generic layout. Let's create another one that will will be used just for posts. This time we will use [Jade](http://jade-lang.com/) as a template engine.

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
Let's see how the inheritance works. Fist Mikser renders the Markdown document content. Then it renders the layout defined in the document meta data. In this case this is `/post.jade`. As you can see we are using the document meta data and the content inside this layout. Then the result of this render is passed to layout defined in the `/post.jade` in our case this is `/html5.ect`. Now when we refer to the content inside `/html5.ect` it will be the result of  the rendered `/post.jade`.

Lets create another document this time an YAML document representing an item in an on-line shop and create a layout for this kind of documents using a [Swig](http://paularmstrong.github.io/swig/) template engine.

```yaml
layout: /shop/item.swig
title: Item title
description: Item description
features:
- Feature 1
- Feature 2
- Feature 3
```

Here is the `/shop/item.swig` layout, it uses `/html5.ect` as a parent layout. We can group layouts in folders and we will put this one in a folder called `shop` inside `layouts` folder.

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

As you can see when the document has no content we work only with the meta data. This will render first the `/shop/item.swig` layout using meta data from the document and then `/html5.ect` layout which will get the result of the `/shop/item.swig` render in the `content` property.

### Using data queries
#### Layout queries
#### Context queries
#### Sorting
### Referring other documents from layouts
### Blocks/Partials
#### Using plain blocks
#### Using blocks with options
### Short codes
### Paging
### Using auto layouts
