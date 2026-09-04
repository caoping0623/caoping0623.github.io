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
  /* The field is confined to a quarter disc centred on the top-right corner
     of the viewport. Radius is 2/3 of the page width, trimmed by 20%. */
  var RADIUS_RATIO = (2 / 3) * 0.8;
  var R;
  var NODE_COUNT = 0;
  var MAX_DIST = 185;
  var mouse = { x: -9999, y: -9999 };
  var nodes = [];

  function resize() {
    W = Math.round(window.innerWidth);
    H = Math.round(window.innerHeight);
    R = W * RADIUS_RATIO;
    canvas.width  = W;
    canvas.height = H;
  }

  function desiredCount() {
    /* One node per NODE_AREA px² of field, so density no longer drifts with
       viewport size. Lower value = denser mesh; the cap keeps the O(n²) edge
       pass cheap on very wide screens. */
    var NODE_AREA = 16000;
    var area = Math.PI * R * R / 4;
    return Math.max(8, Math.min(110, Math.round(area / NODE_AREA)));
  }

  function randomPointInField() {
    /* Rejection sampling gives a uniform spread over the visible quarter disc. */
    var maxY = Math.min(R, H);
    for (var i = 0; i < 40; i++) {
      var x = W - Math.random() * R;
      var y = Math.random() * maxY;
      var dx = x - W;
      if (dx * dx + y * y <= R * R) return { x: x, y: y };
    }
    return { x: W - R * 0.3, y: Math.min(R * 0.2, H * 0.5) };
  }

  function makeNode() {
    var big = Math.random() < 0.16;
    var p = randomPointInField();
    return {
      x:  p.x,
      y:  p.y,
      baseR: big ? 5 + Math.random() * 3 : 2 + Math.random() * 2.5,
      r:  0,
      vx: (Math.random() - 0.5) * 0.30,
      vy: (Math.random() - 0.5) * 0.30,
      phase: Math.random() * Math.PI * 2,
      phaseSpeed: 0.018 + Math.random() * 0.018
    };
  }

  /* Bounce off the arc and off the two straight edges (x = W, y = 0). */
  function confine(n) {
    var dx = n.x - W, dy = n.y;
    var d = Math.sqrt(dx * dx + dy * dy);
    if (d > R) {
      var nx = dx / d, ny = dy / d;
      n.x = W + nx * R;
      n.y = ny * R;
      var dot = n.vx * nx + n.vy * ny;
      n.vx -= 2 * dot * nx;
      n.vy -= 2 * dot * ny;
    }
    if (n.x > W) { n.x = W; n.vx = -Math.abs(n.vx); }
    if (n.y < 0) { n.y = 0; n.vy =  Math.abs(n.vy); }
    if (n.y > H) { n.y = H; n.vy = -Math.abs(n.vy); }
  }

  function init() {
    resize();
    NODE_COUNT = desiredCount();
    nodes = [];
    for (var i = 0; i < NODE_COUNT; i++) {
      var n = makeNode();
      n.r = n.baseR;
      nodes.push(n);
    }
  }

  window.addEventListener('resize', function () {
    resize();
    var target = desiredCount();
    while (nodes.length < target) {
      var n = makeNode();
      n.r = n.baseR;
      nodes.push(n);
    }
    while (nodes.length > target) {
      nodes.pop();
    }
    NODE_COUNT = target;
    nodes.forEach(confine);
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

    /* Clip everything to the circle; the canvas edges cut away the other
       three quadrants, leaving exactly the top-right quarter. */
    ctx.save();
    ctx.beginPath();
    ctx.arc(W, 0, R, 0, Math.PI * 2);
    ctx.clip();

    /* ---- update nodes ---- */
    nodes.forEach(function (n) {
      n.x += n.vx;
      n.y += n.vy;
      confine(n);

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

        var alpha = (1 - d / MAX_DIST) * (0.32 + mInf * 0.48);
        ctx.beginPath();
        ctx.strokeStyle = 'rgba(160,136,188,' + alpha.toFixed(3) + ')';
        ctx.lineWidth   = 0.85 + mInf * 1.2;
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

    ctx.restore();

    /* Faint boundary arc so the containment reads as deliberate. */
    ctx.beginPath();
    ctx.arc(W, 0, R, Math.PI / 2, Math.PI);
    ctx.strokeStyle = 'rgba(180,155,200,0.16)';
    ctx.lineWidth = 1;
    ctx.stroke();

    requestAnimationFrame(draw);
  }

  init();
  draw();
}());
</script>

<!-- Category directory (docked on wide screens, slide-out below 1400px) -->
<button class="toc-toggle-btn home-toc-toggle" id="home-toc-toggle" aria-label="打开文章目录">
  <svg width="15" height="15" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round">
    <line x1="3" y1="6" x2="21" y2="6"/>
    <line x1="3" y1="12" x2="21" y2="12"/>
    <line x1="3" y1="18" x2="21" y2="18"/>
  </svg>
  <span class="toc-btn-label">目录</span>
</button>

<nav class="toc-sidebar home-toc" id="home-toc" aria-label="文章分类目录">
  <div class="toc-sidebar-header">
    <span class="toc-sidebar-title">文章目录</span>
    <button class="toc-close-btn home-toc-close" id="home-toc-close" aria-label="关闭目录">×</button>
  </div>
  <ul class="toc-sidebar-list home-toc-list">
    {% assign cats = site.categories | sort %}
    {% for cat in cats %}
    <li class="home-toc-group">
      <button type="button" class="home-toc-cat" aria-expanded="false">
        <svg class="home-toc-caret" width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
          <polyline points="9 6 15 12 9 18"/>
        </svg>
        <span class="home-toc-name">{{ cat[0] }}</span>
        <span class="home-toc-count">{{ cat[1].size }}</span>
      </button>
      <ul class="home-toc-sub">
        {% for post in cat[1] %}
        <li class="toc-item toc-h3">
          <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
        </li>
        {% endfor %}
      </ul>
    </li>
    {% endfor %}
  </ul>
</nav>

<div class="toc-backdrop home-toc-backdrop" id="home-toc-backdrop"></div>

<script>
(function () {
  var toggleBtn = document.getElementById('home-toc-toggle');
  var sidebar   = document.getElementById('home-toc');
  var closeBtn  = document.getElementById('home-toc-close');
  var backdrop  = document.getElementById('home-toc-backdrop');

  function openTOC()  { sidebar.classList.add('toc-open');    backdrop.classList.add('toc-open'); }
  function closeTOC() { sidebar.classList.remove('toc-open'); backdrop.classList.remove('toc-open'); }

  toggleBtn && toggleBtn.addEventListener('click', openTOC);
  closeBtn  && closeBtn.addEventListener('click', closeTOC);
  backdrop  && backdrop.addEventListener('click', closeTOC);

  document.addEventListener('keydown', function (e) {
    if (e.key === 'Escape') closeTOC();
  });

  /* Follow a link on the narrow-screen panel and the panel should get out of the way. */
  sidebar && sidebar.addEventListener('click', function (e) {
    if (e.target.closest('a') && window.innerWidth < 1400) closeTOC();
  });

  /* Categories start collapsed; CSS reveals the sub-list from aria-expanded. */
  sidebar && sidebar.querySelectorAll('.home-toc-cat').forEach(function (btn) {
    btn.addEventListener('click', function () {
      var expanded = btn.getAttribute('aria-expanded') === 'true';
      btn.setAttribute('aria-expanded', expanded ? 'false' : 'true');
    });
  });
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
