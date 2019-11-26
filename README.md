# haumea [![PyPI version](https://badge.fury.io/py/haumea.svg)](https://badge.fury.io/py/haumea)

Small &amp; fast python static site generator (SSG) optimized for external JSON (REST API, GraphQL etc.) data driven contents.

Work in progress...

Haumea requires **Python >=3.6**

## Installation

```bash
$ pip install haumea
```

## Quickstart

You can create a skeleton project with the haumea-quickstart command

```bash
$ haumea-quickstart yourprojectname
```

```bash
yourprojectname
├── content		# All content for your website will live inside this directory
│   └── (pages)
├── layouts		# Stores .html templates files
│   └── partials
│   	└── footer.html
│   	└── header.html
│   	└── head.html
│   └── _base.html
├── public		# Your site will be rendered into this dir
└── static		# Stores all the static content: images, CSS, JavaScript, etc.

```

Build & test your website

```bash
$ cd yourprojectname/
$ haumea serve
```

Or just build

```bash
$ cd yourprojectname/
$ haumea build
```

## Documentation

### Directory Structure (sample)

```bash
yourprojectname
├── content
│   └── index.html			# Simple page
│   └── about.html			# Simple page
│   	└── blog             		# Subdir
│   	    └── post-1.html     	# Simple page
│	    └── post-2.html   		# Simple page
│	    └── post-3.html 		# Simple page
│   └── products/        		# Subdir
│	    └── _product.html 		# Page bundle (create from a json array of data)
│	    └── _product.graphql	# graphql query (for json-request-type="graphql")
├── layouts
│   └── partials
│   	└── footer.html
│   	└── header.html
│   	└── head.html
│   └── _base.html
├── public
└── static
```

### Templating tags (layouts dir)

```bash
# render your content into template
{{ _content }} 	

# basic template include
{% include "partials/header.html" %}	

# basic render of menu
{% menu mymainmenu %}	

    # html output
    <ul>
        <li><a href="">item 1</a></li>
        <li><a href="">item 1</a></li>
        <li><a href="">item 1</a></li>
    </ul>

# advanced render of menu
{% menu myfootermenu a.item %}

    # html output
    <a class="item" href="">item 1</a>
    <a class="item" href="">item 1</a>
    <a class="item" href="">item 1</a>

# advanced render of menu
{% menu mycustommenu nav.menu>div.item>a.link.is-active %}

    # html output
    <nav class="menu">
        <div class="item">
            <a class="link is-active" href="">item 1</a>
        </div>
        <div class="item">
            <a class="link" href="">item 1</a>
        </div>
        <div class="item">
            <a class="link" href="">item 1</a>
        </div>
    </nav>

# custom render of menu
{% for menu in _menus.main %}
    <li><a href="{{ menu.page.permalink }}">{{ menu.page._params.title }} - {{ menu.page._json_.fields.regular_price|{:.2f} }}</a></li>
{% endfor %}

# basic render of current timestamp
{% time %}		

{{ _params.title }}
{{ _json_.short_title }}
{{ _json.regular_price|{:.2f} }}
```

### Content config

#### Static page

**page.html**

```bash
---
{
    "title":"Welcome home",
    "menus":["main", "footer"]
}
---
```

#### Single page from JSON or REST API

**page.html**

```bash
---
{
    "json-source" : "https://api.buttercms.com/v2/pages/*/sample-page/",
    "json-request-type" : "get",
    "json-params" : { "locale" : "fr" , "auth_token" : "XXXXXXX" },
    "json-root-node" : "data",

    "title" : "{{ _json.fields.title }} - {{ _json.fields.product_qty }}",
    "menus" : [ "main" ],
    "slug" : "test"
}
---
```

#### Page bundle from JSON with GraphQL

**\_page.html**

```bash
---
{
    "json-source" : "https://graphql.datocms.com/",
    "json-request-type" : "graphql",
    "json-headers" : {"Authorization":"token xxxxxxx"},
    "json-root-node" : "data.allProduits",

    "title": "{{ _json.title }}",
    "nav-title": "Navigation Title !",
    "meta-desc" : "{{ _json.meta_description }}",
    "meta-title" : "{{ _json.meta_title }}",
    "slug" : "{{ _json.slug }}",

    "menus" : [ "products", "footer" ]
}
---
```

**\_page.graphql**

```bash
---
query {
    launchesPast(limit: 80) {
        mission_name
        details
        launch_date_local
        ships {
            name
            image
        }
    }
}
---
```
