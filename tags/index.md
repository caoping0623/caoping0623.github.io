---
layout: default
title: 标签
permalink: /tags/
---

<div class="container" style="padding-top: 3rem;">
  <h1 style="font-size: 2rem; font-weight: 800; margin-bottom: 2rem;">所有标签 🏷️</h1>
  <div class="tag-cloud">
    {% assign tags = site.tags | sort %}
    {% for tag in tags %}
    <a href="#{{ tag[0] }}">{{ tag[0] }} <sup>{{ tag[1].size }}</sup></a>
    {% endfor %}
  </div>

  {% assign tags = site.tags | sort %}
  {% for tag in tags %}
  <div id="{{ tag[0] }}" style="margin-top: 2rem;">
    <h2 class="section-heading">{{ tag[0] }}</h2>
    <div class="post-grid">
      {% for post in tag[1] %}
      <article class="post-card" style="padding: 1rem 1.25rem;">
        <div class="post-card-meta">
          <span>{{ post.date | date: "%Y-%m-%d" }}</span>
        </div>
        <h2 style="font-size: 1rem;"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
      </article>
      {% endfor %}
    </div>
  </div>
  {% endfor %}
</div>
