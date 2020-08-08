---
layout: page
title: Navigation
description: Here is the information you need
permalink: /archive/
---

### Blogs
{% for post in site.posts %}
<div class="post-preview">
    <font color="blue">[{{ post.date | date: "%B %-d, %Y" }}]  </font>
     <a target="_blank" href="{{ post.url | prepend: site.baseurl }}"> {{ post.title }}  </a>
</div>
<hr>
{% endfor %}
