---
layout: page
title: Basics
description: >
  This chapter covers the basics of content creation with Hydejack.
hide_description: true
sitemap: false
---

This chapter covers the basics of content creation with Hydejack.

0. this unordered seed list will be replaced by toc as unordered list
{:toc}


## Adding images
Adding good images is key to a engaging blog experience. You can provide an `image` attribute in in the front matter of posts, pages, and projects* that will be used by Hydejack in a variety of ways, 
such as header image in the `blog` and `post` layout, social media previews, cards in the `gird` and `projects` layout\*, thumbnails in the search dropdown\*, etc.

The `image` attribute will accept an URL to an image, but it is recommended that you provide a `path` / `srcset` hash instead, e.g. 

```yml
image:
  path:    /assets/img/projects/hyde-v2.jpg
  srcset:
    1920w: /assets/img/projects/hyde-v2.jpg
    960w:  /assets/img/projects/hyde-v2@0,5x.jpg
    480w:  /assets/img/projects/hyde-v2@0,25x.jpg
```

Hydejack will show the image in various sizes depending on available screen width so that no specific size will fit all. 
Instead, I recommend using a [mipmap]-like approach, providing the image in multiple sizes, each image half the width of the previous one.
Since Hydejack provides an appropriate [`sizes` attribute][mdn-sizes], the browser can chose the best image from the provided source set.

If you have [ImageMagick] installed, you can use the following commands to create images at 50%, 25%, and 12.5% of the original image. 
Other image tools will provide similar capabilities.

    convert your-image.jpg -resize 50% -sampling-factor 4:2:0 -strip -quality 85 -interlace JPEG -colorspace RGB your-image@0,5x.jpg
    convert your-image.jpg -resize 25% -sampling-factor 4:2:0 -strip -quality 85 -interlace JPEG -colorspace RGB your-image@0,25x.jpg
    convert your-image.jpg -resize 12.5% -sampling-factor 4:2:0 -strip -quality 85 -interlace JPEG -colorspace RGB your-image@0,125x.jpg

Note that the keys in the `srcset` hash have to be valid "descriptors" (as defined [here][mdn-srcset]). In practice this means the width in pixels followed by `w`.

The `path` key is a fallback image for browsers that don't support the `srcset` attribute. It's also used by `jekyll-seo-tag` for social media previews.

For more information on `srcset`, see the [documentation at MDN][mdn-srcset], or [this article from CSS-Tricks][csstricks].

[imagemagick]: https://imagemagick.org/index.php
[mipmap]: https://en.wikipedia.org/wiki/Mipmap
[mdn-srcset]: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#attr-srcset
[mdn-sizes]: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#attr-sizes
[csstricks]: https://css-tricks.com/responsive-images-youre-just-changing-resolutions-use-srcset/



## Adding an entry to the sidebar
To add links to the sidebar, populate the `menu` entry in `_config.yml` with a list of `title`-`url` pairs, e.g.:

```yml
# file: `_config.yml`
menu:
  - title: Blog
    url:   /blog/
  - title: Projects
    url:   /projects/
  - title: Resume
    url:   /resume/
  - title: About
    url:   /about/
```

### Adding a link to an external page to the sidebar
To add links to external sites, simply provide a fully qualified URL, e.g.

```yml
menu:
  - title: "@qwtel"
    url:   https://qwtel.com/
``` 

## Adding a category or tag
Hydejack allows you to use the `list` or `grid`\* layout to show all posts of a particular category or tag.

Before you start, make sure your config files contains the `features_categories` and `featured_tags` collections:

~~~yml
# file: `_config.yml`
collections:
  featured_categories:
    permalink:         /:name/
    output:            true
  featured_tags:
    permalink:         /tag-:name/
    output:            true
~~~

### Recap: Categories and tags in Jekyll
Posts in Jekyll can belong to one or more categories, as well as one or more tags. They are defined in a post's front matter:

~~~yml
---
layout:     post
title:      Welcome to Jekyll
categories: [jekyll, update]
tags:       [jekyll, update]
---
~~~

Posts can also be assigned to a category based on their position within the folder structure, e.g.

~~~
├── jekyll
│   └── update
│       └── _posts
│           └── 2017-04-07-welcome-to-jekyll.markdown
~~~

This will place "Welcome to Jekyll" in the categories `jekyll` and `update`.

This is now the preferred way of assigning categories in Hydejack, as it makes URLs correspond more naturally to the underlying folder structure.
{:.note}

Whether you use this method or not, categories will always be part of a posts URL, while tags will not.

Type       | URL
-----------|----
Categories | `/jekyll/update/2017-04-07-welcome-to-jekyll/`
Tags       | `/2017-04-07-welcome-to-jekyll/`
{:.scroll-table-small}

As far as Jekyll is concerned, this is the only difference.

### Categories and tags in Hydejack
Categories and tags are displayed by Hydejack below the title, after the date. Categories are displayed with the preposition "in", while tags are displayed with the preposition "on", e.g.

Type       | Title
-----------|------
Categories | Welcome to Jekyll¬ 07 Apr 2017 **in** Jekyll / Update
Tags       | Welcome to Jekyll¬ 07 Apr 2017 **on** Jekyll, Update
Both       | Welcome to Jekyll¬ 07 Apr 2017 **in** Jekyll / Update **on** Jekyll, Update
{:.scroll-table-small}

You can adjust these in [`_data/string.yml`][strings].

### Creating a new category or tag
By default, categories and tags are rendered as plain text. Further steps are necessary if you want them to link to a page that contains a list of all posts that belong to that category or tag.

For each featured category or tag, a file called `<category-name>.md` or `<tag-name>.md` has to be created inside the `_featured_tags` and `_featured_categories` folders, respectively. Each file in these folders is part of a [Jekyll Collection](https://jekyllrb.com/docs/collections/).

The meta data of a category or tag is set in the files front matter, e.g.

~~~yml
# file: `_featured_categories/hyde.md`
---
layout: list
title:  Hyde
slug:   hyde
description: >
  Hyde is a brazen two-column [Jekyll](http://jekyllrb.com) theme.
  It's based on [Poole](http://getpoole.com), the Jekyll butler.
---
~~~

`layout`
: Must either `list` or `grid`\*

`title`
: Used as title of the page, as well as name of the category or tag as part of the line below a blog post's title. Can be different from the name of the tag or category, as long as `slug` is identical to the name.

`slug`
: Must be identical to the key used in the blog's front matter, i.e. if you use `categories: [jekyll]` the `slug` must be `jekyll`. By default, the slug is derived from the title, but here it is recommended that you set it explicitly.

`description`
: A medium-length description, used on the tag or category's detail page and shown in a message box below the title.

`menu`
: Set to to `true` if you want the category or tag to appear in the sidebar. For more information, see [Adding an entry to the sidebar](#adding-an-entry-to-the-sidebar).

Once the file is created, the page can be found at `/category/<categoryname>/` or `/tag/<tagname>/`.


## Adding an about page
About pages are a frequent use case, so Hydejack has a special layout for it. It is a slight modification of the `page` layout that allows showing the author information by adding the `<!--author-->` marker somewhere on the page.

To create an about page, make sure `layout` is set to `about`.
For more on authors, see [Adding an author](config.md#adding-an-author).

~~~md
<!-- file: `about.md` -->
---
layout: about
title:  About
---

Some content

<!--author-->
~~~


## Adding a cover page
Hydejack 8 introduces cover pages, i.e. pages witht he sidebar opened, so that it spans the entire screen. This feature is intended for landing pages. To enable it on a page, simply add `cover: true` to the front matter.

![Cover page example](../assets/img/blog/hydejack-8@0,5x.png){:.lead width="960" height="540" loading="lazy"}

~~~yml
# file: `index.md`
---
layout: welcome
title:  Welcome
cover:  true #!! Add this
---
~~~

## Adding related posts to a post
You can choose which posts will appear in the "Related Posts" section below a post by adding the `related_posts` key to the front matter of a post

~~~yml
# file: `category/_posts/2020-02-01-some-post.md`
---
layout: post
related_posts:
  # Specify via the path in the file system
  - category/_posts/2020-01-01-other-post.md
  # Can also use the url of the post,
  # but this will break when changing the `permalink` setting!
  - /blog/category/2020-01-02-other-other-post/
---
~~~

## Customization
### Adding custom CSS
The quickest and safest way to add custom CSS to Hydejack is via the `_sass/my-inline.scss` and `_sass/my-style.scss` files (create the folder/the files if they don't exist).

To add CSS that gets inlined into the page, i.e. is loaded with the first request, put the CSS rules into `my-inline.scss`. This is intended for above-the-fold content. Otherwise put the CSS rules into `my-style.scss`.
Note that this distinction has no effect when `no_inline_css` is enabled.


### Adding custom HTML to the head
To add custom HTML elements to the `<head>` of the document, open `_includes/my-head.html` (create the folder/the files if they don't exist) and add your elements there.


### Adding custom HTML to the body
To add custom HTML elements to the `<body>` of the document, open `_includes/my-body.html` (create the folder/the files if they don't exist) and add your elements there.

What's the difference to `my-scripts.html`?
: This file was used in earlier versions of Hydejack to accomplish the same goal. However, there are still instances were you might want to prefer `my-scripts.html` over `my-body.html`, as it won't load scrips on redirect pages and will be ignored by browsers < IE10.

Continue with [Writing](writing.md){:.heading.flip-title}
{:.read-more}

[welcome]: https://hydejack.com/
[resume]: https://hydejack.com/resume/
[projects]: https://hydejack.com/projects/
[project]: https://hydejack.com/projects/default/

[strings]: https://github.com/hydecorp/hydejack-site/blob/master/_data/strings.yml
[resumeyml]: https://github.com/hydecorp/hydejack-site/blob/master/_data/resume.yml
