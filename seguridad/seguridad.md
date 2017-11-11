---
layout: page
title: Seguridad
permalink: /seguridad/
---

<div class="home">

  <ul class="posts">
    {% for post in site.posts %}
    {% if post.categories contains 'seguridad' or post.tags contains 'seguridad' %}
      <li>
<!--        <span class="post-date">{{ post.date | date: "%b %-d, %Y" }}</span> -->
        <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
      </li>
    {% endif %}
    {% endfor %}
  </ul>

  <p class="rss-subscribe">Subscripci&oacute;n <a href="{{ "/feed.xml" | prepend: site.baseurl }}">v&iacute;a RSS</a></p>

</div>
