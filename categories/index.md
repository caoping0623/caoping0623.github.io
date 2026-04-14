---
layout: default
title: 分类
permalink: /categories/
---

<div class="container" style="padding-top: 3rem;">
  <h1 style="font-size: 2rem; font-weight: 800; margin-bottom: 2rem;">文章分类 📂</h1>
  <div class="category-list">
    {% assign categories = site.categories | sort %}
    {% for category in categories %}
    <div class="category-group">
      <h2>{{ category[0] }} <span class="category-count">{{ category[1].size }} 篇</span></h2>
      <div class="post-grid">
        {% for post in category[1] %}
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
</div>
