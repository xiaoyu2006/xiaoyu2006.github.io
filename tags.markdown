---
layout: page
title: Tags
permalink: /tags/
---

{% assign tags = site.tags | sort %}

<div class="post-preview">
  {% for tag in tags %}
    <h2 style="padding-top: 70px;"> <a href="/tag/{{ tag[0] }}.html">{{ tag[0] }}</a>  <small>posts: {{ tag | last | size }}</small></h2>
    <ul>
    {% for post in tag[1] %}
      <a href="{{ site.baseurl }}{{ post.url }}">
      <li>
        {{ post.title }}
        <small> - Posted on {{ post.date | date: "%B %-d, %Y" }}</small>
      </li>
      </a>
    {% endfor %}
    </ul>
    <hr/>
  {% endfor %}
</div>
