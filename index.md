---
layout: default
permalink: /
---

<canvas id="neuro-canvas" aria-hidden="true"></canvas>

<script>
(function () {
  var canvas = document.getElementById('neuro-canvas');
  var ctx = canvas.getContext('2d');

  var W, H;
  var NODE_COUNT = 32;
  var MAX_DIST = 130;
  var mouse = { x: -9999, y: -9999 };
  var nodes = [];

  function resize() {
    W = Math.round(window.innerWidth);
    H = Math.round(window.innerHeight);
    canvas.width  = W;
    canvas.height = H;
  }

  function makeNode() {
    var big = Math.random() < 0.18;
    return {
      x:  Math.random() * W,
      y:  Math.random() * H,
      baseR: big ? 5 + Math.random() * 3 : 2 + Math.random() * 2.5,
      r:  0,
      vx: (Math.random() - 0.5) * 0.28,
      vy: (Math.random() - 0.5) * 0.28,
      phase: Math.random() * Math.PI * 2,
      phaseSpeed: 0.018 + Math.random() * 0.018
    };
  }

  function init() {
    resize();
    nodes = [];
    for (var i = 0; i < NODE_COUNT; i++) {
      var n = makeNode();
      n.r = n.baseR;
      nodes.push(n);
    }
  }

  window.addEventListener('resize', function () {
    resize();
    nodes.forEach(function (n) {
      n.x = Math.min(n.x, W);
      n.y = Math.min(n.y, H);
    });
  });

  window.addEventListener('mousemove', function (e) {
    var rect = canvas.getBoundingClientRect();
    mouse.x = e.clientX - rect.left;
    mouse.y = e.clientY - rect.top;
  });

  window.addEventListener('mouseleave', function () {
    mouse.x = -9999;
    mouse.y = -9999;
  });

  function dist2(ax, ay, bx, by) {
    var dx = ax - bx, dy = ay - by;
    return Math.sqrt(dx * dx + dy * dy);
  }

  function draw() {
    ctx.clearRect(0, 0, W, H);

    /* ---- update nodes ---- */
    nodes.forEach(function (n) {
      n.x += n.vx;
      n.y += n.vy;
      if (n.x < 0)  { n.x = 0;  n.vx *= -1; }
      if (n.x > W)  { n.x = W;  n.vx *= -1; }
      if (n.y < 0)  { n.y = 0;  n.vy *= -1; }
      if (n.y > H)  { n.y = H;  n.vy *= -1; }

      n.phase += n.phaseSpeed;
      var mdist   = dist2(n.x, n.y, mouse.x, mouse.y);
      var mInfluence = Math.max(0, 1 - mdist / 75);
      var target  = n.baseR * (1 + 0.18 * Math.sin(n.phase)) + mInfluence * n.baseR * 2.2;
      n.r += (target - n.r) * 0.14;
    });

    /* ---- draw edges ---- */
    for (var i = 0; i < nodes.length; i++) {
      for (var j = i + 1; j < nodes.length; j++) {
        var a = nodes[i], b = nodes[j];
        var d = dist2(a.x, a.y, b.x, b.y);
        if (d >= MAX_DIST) continue;

        var mda = dist2(a.x, a.y, mouse.x, mouse.y);
        var mdb = dist2(b.x, b.y, mouse.x, mouse.y);
        var mInf = Math.max(0, 1 - Math.min(mda, mdb) / 100);

        var alpha = (1 - d / MAX_DIST) * (0.22 + mInf * 0.45);
        ctx.beginPath();
        ctx.strokeStyle = 'rgba(160,136,188,' + alpha.toFixed(3) + ')';
        ctx.lineWidth   = 0.7 + mInf * 1.1;
        ctx.moveTo(a.x, a.y);
        ctx.lineTo(b.x, b.y);
        ctx.stroke();
      }
    }

    /* ---- draw nodes ---- */
    nodes.forEach(function (n) {
      var mdist = dist2(n.x, n.y, mouse.x, mouse.y);
      var near  = mdist < 65;

      if (near) {
        /* soft glow */
        var grd = ctx.createRadialGradient(n.x, n.y, n.r * 0.4, n.x, n.y, n.r * 3.2);
        grd.addColorStop(0, 'rgba(122,184,181,0.22)');
        grd.addColorStop(1, 'rgba(122,184,181,0)');
        ctx.beginPath();
        ctx.arc(n.x, n.y, n.r * 3.2, 0, Math.PI * 2);
        ctx.fillStyle = grd;
        ctx.fill();
      }

      ctx.beginPath();
      ctx.arc(n.x, n.y, n.r, 0, Math.PI * 2);
      if (near) {
        ctx.fillStyle = '#7ab8b5';
      } else if (n.baseR > 6) {
        ctx.fillStyle = 'rgba(180,155,200,0.92)';
      } else {
        ctx.fillStyle = 'rgba(180,155,200,0.58)';
      }
      ctx.fill();

      ctx.beginPath();
      ctx.arc(n.x, n.y, n.r, 0, Math.PI * 2);
      ctx.strokeStyle = near ? 'rgba(122,184,181,0.75)' : 'rgba(180,155,200,0.45)';
      ctx.lineWidth   = 0.9;
      ctx.stroke();
    });

    requestAnimationFrame(draw);
  }

  init();
  draw();
}());
</script>

<div class="container">
  <section class="hero">
    <div class="hero-avatar"><img src="{{ '/assets/css/头像.png' | relative_url }}" alt="avatar"></div>
    <h1><span>Cao Ping</span></h1>
    <p>持续进化</p>
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
