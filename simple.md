---
layout: base
title: My notes
---

<article>
<h1>Recent</h1>
<ul>
{% for post in site.posts limit:20 %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>
<h1>How to</h1>
<ul>
{% for page in site.pages %}
    {% if page.url contains '/howto/' %}
        <li><a href="{{ page.url }}">{{ page.title }}</a></li>
    {% endif %}
{% endfor %}
</ul>
</article>
