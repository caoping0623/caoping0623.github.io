---
layout: default
permalink: /
---

<div class="container">
  <section class="hero">
    <div class="hero-avatar">👨‍💻</div>
    <h1>你好，我是 <span>Cao Ping</span></h1>
    <p>热爱技术、持续学习。在这里分享编程经验、架构思考与工程实践。</p>
    <div class="hero-badges">
      <span class="badge">🐍 Python</span>
      <span class="badge">☕ Java</span>
      <span class="badge">🐹 Go</span>
      <span class="badge">⚛️ React</span>
      <span class="badge">🐳 Docker</span>
      <span class="badge">☁️ Cloud Native</span>
    </div>
  </section>

  <h2 class="section-heading">最新文章</h2>

  <div class="post-grid">
    {% for post in site.posts limit:10 %}
    <article class="post-card">
      <div class="post-card-meta">
        {% if post.categories.size > 0 %}
        <span class="post-card-category">{{ post.categories | first }}</span>
        {% endif %}
        <span>{{ post.date | date: "%Y-%m-%d" }}</span>
      </div>
      <h2><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
      {% if post.excerpt %}
      <p>{{ post.excerpt | strip_html | truncate: 120 }}</p>
      {% endif %}
      {% if post.tags.size > 0 %}
      <div class="post-tags">
        {% for tag in post.tags %}
        <span class="tag-chip">{{ tag }}</span>
        {% endfor %}
      </div>
      {% endif %}
    </article>
    {% endfor %}
  </div>

  {% if site.posts.size == 0 %}
  <p style="text-align:center; color: var(--color-text-muted); padding: 3rem 0;">暂无文章，敬请期待…</p>
  {% endif %}
</div>
