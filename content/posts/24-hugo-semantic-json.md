---
title: "How to add Semantic Web JSON-LD to Hugo website"
date: 2024-10-14T11:39:09+03:00
tags:
    - Hugo
keywords:
    - Hugo
    - Semantic Web
    - JSON-LD
---

<!--more-->

# What is Semantic Web

Semantic web is a part of so-called Web 3.0 concept. It's all about adding additional metadata to the webpages so
they can be machine-readable. This allows things like search engine crawlers and social media platforms to better 
understand and parse the pages allowing them to show better links and previews. You can see how metadata works with Google
in their [developer page](https://developers.google.com/search/docs/appearance/structured-data/intro-structured-data)

# What about JSON-LD

JSON-LD is a type of metadata json you can add to the `<head>` section of your page. It has a schema definition and 
concrete format. You can read more about it in the [oficial document](https://www.w3.org/TR/2014/REC-json-ld-20140116/).
Here is a brief example of how JSON-LD looks like:
``` json
<script type="application/ld+json">
    {
        "@context": "https://schema.org",
        "@type": "BlogPosting",
        "headline": "How to add Semantic Web JSON-LD to Hugo website",
        "datePublished": "2024-10-14",
        "mainEntityOfPage": {
            "@type": "WebPage",
            "@id": "https://some-url.com"
        }
    }
</script>
```

There is a lot of supported types and properties, you can check them all at https://schema.org

# How to add JSON-LD to Hugo

So this blog is created with Hugo static site generator. Since it's very simple blog it's best to add
the metadata manually. There are some Hugo plugins that does it for you, but they seem to complex for my purposes.

## Step 1: find how to change the `<head>` element

I am using a theme with Hugo and my theme is configured to insert a partial called "extended_head" into the `<head>` element of the page.
So I just needed to create that partial and just insert my code there. If you use different theme, the best way is to either check 
the theme docs for extension points or looked through the code of the theme and modify it.

## Step 2: conditions

Since my website `<head>` is the same for every page and I want to include the metadata where it relevant I have to add 
some conditional rendering.

You can use either the type of the page: `{{ if eq .Type "posts" }}` or the relative url `{{ if eq .RelPermalink "/about/" }}`.
Or of course you can write your own condition using the Hugo templating syntax.

## Step 3: front matter 
In order to generate the correct metadata for each post we need to take it from somewhere. In Hugo best way to do that is by 
using markdown front matter. So add some to your post:
``` markdown
---
title: "How to add Semantic Web JSON-LD to Hugo website"
date: 2024-10-14T11:39:09+03:00
---
```

## Step 4: build the JSON
So here is how it looks like for my site:
``` json
{{ if eq .Type "posts" }}
<script type="application/ld+json">
    {
        "@context": "https://schema.org",
        "@type": "BlogPosting",
        "headline": "{{ .Params.title }}",
        "datePublished": "{{ .Params.date }}",
        "author": {
            "@type": "Person",
            "name": "1v0",
            "url": "https://1v0.dev"
        },
            {{ with .Params.cover }}
            "image": {
                "@type": "ImageObject",
                "url": "{{ . | absURL }}"
            },
            {{ end }}
        "mainEntityOfPage": {
            "@type": "WebPage",
            "@id": "{{ .Permalink }}"
        }
    }
</script>
{{ end }}
```

We get the front matter data from the post using .Params `{{ .Params.title }}`.

Also, we can do conditionals inside the json so we can include the image only if it exists.

## Step 5: check if works

Once your website is ready, you can check if the metadata works and is correct by using 
[Google's Rich Results Test](https://search.google.com/test/rich-results)

Here is an example from this blog:
![test](/posts/images/24-rich-results-test.jpg)