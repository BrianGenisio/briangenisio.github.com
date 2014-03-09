---
layout: page
title: Recent Posts
tagline: Supporting tagline
---
{% include JB/setup %}

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a>

    {% if forloop.index < 5 %}
      <div>{{ post.content }}</div></li>
    {% endif %}
    
  {% endfor %}
</ul>

