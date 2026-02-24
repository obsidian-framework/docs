# Pebble Templates

Obsidian uses [Pebble](https://pebbletemplates.io/) as its templating engine. It offers a clean, Twig-like syntax with layouts, blocks, and template functions.

## Basic Template

```twig
{# views/articles/index.html #}
{% extends "layout.html" %}

{% block title %}{{ title }}{% endblock %}

{% block content %}

    <h1>{{ title }}</h1>

    {% for article in articles %}
    <article>
        <h2>{{ article.title }}</h2>
        <p>{{ article.content }}</p>
    </article>
    {% endfor %}

{% endblock %}
```

## Rendering from a Controller

```java
return render("articles/index.html", Map.of(
    "title",    "My articles",
    "articles", articles
));
```
