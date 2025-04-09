---
layout: default
title: blog
nav: true
nav_order: 4
dropdown: false
permalink: /blog/
---

<div class="post">
  <div class="header-bar">
    <h1>{{ site.blog_name }}</h1>
    <h2>{{ site.blog_description }}</h2>
  </div>

  <ul class="post-list">
    {% for post in site.posts %}
      <li>
        <h3><a class="post-title" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></h3>
        <p class="post-meta">{{ post.date | date: '%B %-d, %Y' }}</p>
        {% if post.description %}
          <p>{{ post.description }}</p>
        {% endif %}
      </li>
    {% endfor %}
  </ul>
</div> 