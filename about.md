---
layout: default
title: 关于我
permalink: /about/
---

<div class="container" style="padding-top: 3rem; padding-bottom: 3rem; max-width: 700px;">
  <h1 style="font-size: 2rem; font-weight: 800; margin-bottom: 2rem;">关于我 👋</h1>

  <div style="background: var(--color-surface); border: 1px solid var(--color-border); border-radius: 12px; padding: 2rem; margin-bottom: 2rem;">
    <p style="margin-bottom: 1rem;">
      我是真人，但作者是 <strong>Copilot</strong>！
    </p>
    <p style="margin-bottom: 1rem;">
      Copilot 负责写文章，我负责在它写出死循环的时候拔电源。
    </p>
    <p style="margin-bottom: 1rem;">
      文章有错误，那是 Copilot 叛逆期到了；文章写的漂亮，那是我调教有方！
    </p>
    <p>
      文章质量忽高忽低，别怀疑，那是因为 Copilot 额度快用完了~
    </p>
  </div>

  <h2 style="font-size: 1.1rem; font-weight: 700; color: var(--color-text-muted); margin-bottom: 1rem;">🛠️ 技术栈</h2>
  <div style="display: flex; flex-wrap: wrap; gap: 0.5rem; margin-bottom: 2rem;">
    {% assign skills = "Python,Java,Go,JavaScript,TypeScript,React,Spring Boot,FastAPI,Docker,Kubernetes,Redis,PostgreSQL,MySQL,Kafka,Elasticsearch,Git,Linux,AWS" | split: "," %}
    {% for skill in skills %}
    <span class="badge">{{ skill }}</span>
    {% endfor %}
  </div>

  <h2 style="font-size: 1.1rem; font-weight: 700; color: var(--color-text-muted); margin-bottom: 1rem;">📬 联系方式</h2>
  <div style="background: var(--color-surface); border: 1px solid var(--color-border); border-radius: 12px; padding: 1.5rem;">
    <p>💻 GitHub: <a href="https://github.com/caoping0623" target="_blank" rel="noopener">github.com/caoping0623</a></p>
    <p style="margin-top: 0.75rem; color: var(--color-text-muted); font-size: 0.9rem;">欢迎通过 GitHub Issues 或 PR 与我交流！</p>
  </div>
</div>
