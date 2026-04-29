<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Beat Visualizer — Кейс-стади</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Cormorant:ital,wght@0,300;0,400;0,600;0,700;1,300;1,400&family=Jost:wght@300;400;500;600&family=Bebas+Neue&display=swap" rel="stylesheet">
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --bg: #09080a;
    --bg2: #110f13;
    --gold: #c8a96e;
    --gold-dim: #8a7348;
    --gold-glow: rgba(200, 169, 110, 0.15);
    --text: #e2d9cc;
    --text-dim: #8a8078;
    --line: rgba(200, 169, 110, 0.18);
    --serif: 'Cormorant', Georgia, serif;
    --sans: 'Jost', sans-serif;
    --display: 'Bebas Neue', sans-serif;
  }

  html { scroll-behavior: smooth; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: var(--sans);
    font-weight: 300;
    font-size: 16px;
    line-height: 1.7;
    overflow-x: hidden;
  }

  /* ── GRAIN OVERLAY ── */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 512 512' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.75' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.04'/%3E%3C/svg%3E");
    pointer-events: none;
    z-index: 9999;
    opacity: .4;
  }

  /* ── SCROLLBAR ── */
  ::-webkit-scrollbar { width: 4px; }
  ::-webkit-scrollbar-track { background: var(--bg); }
  ::-webkit-scrollbar-thumb { background: var(--gold-dim); border-radius: 2px; }

  /* ── HERO ── */
  .hero {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: flex-end;
    padding: 0 7vw 8vh;
    position: relative;
    overflow: hidden;
  }

  .hero-bg {
    position: absolute;
    inset: 0;
    background:
      radial-gradient(ellipse 60% 50% at 80% 40%, rgba(200,169,110,0.07) 0%, transparent 60%),
      radial-gradient(ellipse 40% 60% at 10% 80%, rgba(120,80,200,0.05) 0%, transparent 50%),
      linear-gradient(180deg, #09080a 0%, #0e0b10 100%);
  }

  .hero-line {
    position: absolute;
    top: 0; left: 7vw;
    width: 1px;
    height: 100%;
    background: linear-gradient(180deg, transparent 0%, var(--gold-dim) 30%, var(--gold-dim) 70%, transparent 100%);
    opacity: .35;
    animation: linePulse 4s ease-in-out infinite;
  }

  @keyframes linePulse {
    0%, 100% { opacity: .35; }
    50% { opacity: .6; }
  }

  .hero-tag {
    font-family: var(--sans);
    font-size: .7rem;
    font-weight: 500;
    letter-spacing: .25em;
    text-transform: uppercase;
    color: var(--gold);
    margin-bottom: 2.5rem;
    opacity: 0;
    animation: fadeUp .8s .3s ease forwards;
  }

  .hero-title {
    font-family: var(--display);
    font-size: clamp(4.5rem, 13vw, 13rem);
    line-height: .9;
    letter-spacing: .02em;
    color: var(--text);
    opacity: 0;
    animation: fadeUp .9s .5s ease forwards;
  }

  .hero-title span {
    display: block;
    color: var(--gold);
    -webkit-text-stroke: 1px var(--gold);
    color: transparent;
  }

  .hero-subtitle {
    margin-top: 2.5rem;
    max-width: 44ch;
    font-size: 1rem;
    color: var(--text-dim);
    font-weight: 300;
    line-height: 1.8;
    opacity: 0;
    animation: fadeUp .8s .8s ease forwards;
  }

  .hero-meta {
    position: absolute;
    top: 7vh; right: 7vw;
    text-align: right;
    opacity: 0;
    animation: fadeIn 1s 1s ease forwards;
  }

  .hero-meta span {
    display: block;
    font-family: var(--sans);
    font-size: .65rem;
    letter-spacing: .2em;
    text-transform: uppercase;
    color: var(--text-dim);
    margin-bottom: .5rem;
  }

  .hero-meta strong {
    font-family: var(--serif);
    font-size: 1.1rem;
    font-weight: 400;
    color: var(--gold);
  }

  .scroll-hint {
    position: absolute;
    bottom: 3vh; right: 7vw;
    display: flex;
    align-items: center;
    gap: .8rem;
    font-size: .65rem;
    letter-spacing: .2em;
    text-transform: uppercase;
    color: var(--text-dim);
    opacity: 0;
    animation: fadeIn 1s 1.5s ease forwards;
  }

  .scroll-hint::before {
    content: '';
    display: block;
    width: 40px; height: 1px;
    background: var(--gold-dim);
  }

  /* ── ANIMATIONS ── */
  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(30px); }
    to   { opacity: 1; transform: translateY(0); }
  }
  @keyframes fadeIn {
    from { opacity: 0; }
    to   { opacity: 1; }
  }

  /* ── WRAPPER ── */
  .container {
    width: 100%;
    padding: 0 7vw;
  }

  /* ── DIVIDER ── */
  .divider {
    display: flex;
    align-items: center;
    gap: 1.5rem;
    margin: 0 0 4rem;
  }
  .divider::before, .divider::after {
    content: ''; flex: 1;
    height: 1px;
    background: var(--line);
  }
  .divider span {
    font-size: .6rem;
    letter-spacing: .3em;
    text-transform: uppercase;
    color: var(--gold-dim);
  }

  /* ── SECTION LABEL ── */
  .section-label {
    font-size: .65rem;
    letter-spacing: .3em;
    text-transform: uppercase;
    color: var(--gold);
    margin-bottom: 1rem;
  }

  /* ── INTRO SECTION ── */
  .section-intro {
    padding: 12vh 0 10vh;
    border-top: 1px solid var(--line);
  }

  .intro-grid {
    display: grid;
    grid-template-columns: 1fr 2fr;
    gap: 6rem;
    align-items: start;
  }

  .intro-heading {
    font-family: var(--serif);
    font-size: clamp(2.5rem, 4vw, 4rem);
    font-weight: 300;
    line-height: 1.15;
    color: var(--text);
  }

  .intro-heading em {
    font-style: italic;
    color: var(--gold);
  }

  .intro-body p {
    color: var(--text-dim);
    margin-bottom: 1.5rem;
    font-size: 1rem;
    line-height: 1.85;
  }

  .intro-body p:last-child { margin-bottom: 0; }

  /* ── FORMATS SECTION ── */
  .section-formats {
    padding: 10vh 0;
    background: var(--bg2);
    border-top: 1px solid var(--line);
    border-bottom: 1px solid var(--line);
  }

  .formats-header {
    text-align: center;
    margin-bottom: 5rem;
  }

  .formats-header h2 {
    font-family: var(--serif);
    font-size: clamp(2rem, 4vw, 3.5rem);
    font-weight: 300;
    color: var(--text);
    margin-top: .75rem;
  }

  .formats-header h2 em { font-style: italic; color: var(--gold); }

  .formats-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 2.5rem;
  }

  .format-card {
    position: relative;
  }

  .format-label {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 1rem;
  }

  .format-label h3 {
    font-family: var(--display);
    font-size: 1.4rem;
    letter-spacing: .08em;
    color: var(--text);
  }

  .format-label .ratio {
    font-size: .65rem;
    letter-spacing: .2em;
    text-transform: uppercase;
    color: var(--gold);
    border: 1px solid var(--gold-dim);
    padding: .25rem .6rem;
  }

  .format-platform {
    font-size: .7rem;
    letter-spacing: .15em;
    text-transform: uppercase;
    color: var(--text-dim);
    margin-bottom: 1.2rem;
  }

  .video-wrap {
    position: relative;
    background: #000;
    overflow: hidden;
    border: 1px solid var(--line);
  }

  .video-wrap::before {
    content: '';
    position: absolute;
    inset: 0;
    background: linear-gradient(135deg, var(--gold-glow) 0%, transparent 60%);
    pointer-events: none;
    z-index: 1;
    opacity: 0;
    transition: opacity .4s;
  }

  .format-card:hover .video-wrap::before { opacity: 1; }

  .video-wrap.ratio-16-9 { aspect-ratio: 16/9; }
  .video-wrap.ratio-9-16 { aspect-ratio: 9/16; }
  .video-wrap.ratio-1-1  { aspect-ratio: 1/1; }

  .video-wrap iframe {
    width: 100%; height: 100%;
    border: none;
    display: block;
  }

  .format-desc {
    margin-top: 1rem;
    font-size: .85rem;
    color: var(--text-dim);
    line-height: 1.7;
  }

  /* ── INSIGHTS ── */
  .section-insights {
    padding: 12vh 0;
  }

  .insights-header {
    margin-bottom: 6rem;
  }

  .insights-header h2 {
    font-family: var(--serif);
    font-size: clamp(2rem, 4vw, 3.5rem);
    font-weight: 300;
    line-height: 1.2;
    color: var(--text);
    max-width: 16ch;
  }

  .insights-header h2 em { font-style: italic; color: var(--gold); }

  .insight-block {
    display: grid;
    grid-template-columns: 80px 1fr;
    gap: 3rem;
    align-items: start;
    padding: 3.5rem 0;
    border-top: 1px solid var(--line);
    position: relative;
    transition: padding-left .4s ease;
  }

  .insight-block:last-child { border-bottom: 1px solid var(--line); }

  .insight-block:hover { padding-left: 1rem; }

  .insight-num {
    font-family: var(--display);
    font-size: 3.5rem;
    line-height: 1;
    color: var(--gold-dim);
    letter-spacing: .05em;
    opacity: .5;
    transition: opacity .3s, color .3s;
  }

  .insight-block:hover .insight-num {
    opacity: 1;
    color: var(--gold);
  }

  .insight-content h3 {
    font-family: var(--serif);
    font-size: 1.6rem;
    font-weight: 600;
    color: var(--text);
    margin-bottom: 1rem;
    line-height: 1.3;
  }

  .insight-content p {
    color: var(--text-dim);
    font-size: .95rem;
    line-height: 1.85;
    margin-bottom: 1rem;
  }

  .insight-content p:last-child { margin-bottom: 0; }

  /* tags */
  .tag-list {
    display: flex;
    flex-wrap: wrap;
    gap: .5rem;
    margin-top: 1.5rem;
  }

  .tag {
    font-size: .65rem;
    letter-spacing: .15em;
    text-transform: uppercase;
    color: var(--gold-dim);
    border: 1px solid var(--gold-dim);
    padding: .3rem .75rem;
    transition: color .2s, border-color .2s;
  }

  .insight-block:hover .tag {
    color: var(--gold);
    border-color: var(--gold);
  }

  /* ── PULLQUOTE ── */
  .pullquote-wrap {
    padding: 10vh 0;
    border-top: 1px solid var(--line);
    border-bottom: 1px solid var(--line);
    background: var(--bg2);
    overflow: hidden;
    position: relative;
  }

  .pullquote-wrap::before {
    content: '"';
    position: absolute;
    top: -2rem; left: 5vw;
    font-family: var(--serif);
    font-size: 30vw;
    color: var(--gold);
    opacity: .03;
    line-height: 1;
    pointer-events: none;
  }

  .pullquote {
    max-width: 720px;
    margin: 0 auto;
    text-align: center;
    padding: 0 7vw;
  }

  .pullquote blockquote {
    font-family: var(--serif);
    font-size: clamp(1.4rem, 2.8vw, 2.2rem);
    font-weight: 300;
    font-style: italic;
    line-height: 1.5;
    color: var(--text);
    margin-bottom: 2.5rem;
  }

  .pullquote blockquote strong {
    font-style: normal;
    color: var(--gold);
    font-weight: 600;
  }

  .pullquote cite {
    font-size: .65rem;
    letter-spacing: .3em;
    text-transform: uppercase;
    color: var(--text-dim);
    font-style: normal;
  }

  /* ── RESULT ── */
  .section-result {
    padding: 12vh 0;
  }

  .result-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 5rem;
    align-items: center;
  }

  .result-text h2 {
    font-family: var(--serif);
    font-size: clamp(2rem, 3.5vw, 3.2rem);
    font-weight: 300;
    line-height: 1.2;
    color: var(--text);
    margin-bottom: 2rem;
  }

  .result-text h2 em { font-style: italic; color: var(--gold); }

  .result-text p {
    color: var(--text-dim);
    font-size: .95rem;
    line-height: 1.85;
    margin-bottom: 1.2rem;
  }

  .result-stats {
    display: flex;
    flex-direction: column;
    gap: 2rem;
  }

  .stat-row {
    display: flex;
    align-items: center;
    gap: 1.5rem;
    padding: 1.5rem 2rem;
    border: 1px solid var(--line);
    background: var(--bg2);
    position: relative;
    overflow: hidden;
    transition: border-color .3s;
  }

  .stat-row::before {
    content: '';
    position: absolute;
    left: 0; top: 0; bottom: 0;
    width: 3px;
    background: var(--gold);
    transform: scaleY(0);
    transform-origin: bottom;
    transition: transform .4s ease;
  }

  .stat-row:hover::before { transform: scaleY(1); }
  .stat-row:hover { border-color: var(--gold-dim); }

  .stat-icon {
    font-family: var(--display);
    font-size: 2.2rem;
    color: var(--gold);
    min-width: 50px;
    text-align: center;
    letter-spacing: .05em;
  }

  .stat-info h4 {
    font-family: var(--serif);
    font-size: 1.1rem;
    font-weight: 600;
    color: var(--text);
    margin-bottom: .25rem;
  }

  .stat-info p {
    font-size: .8rem;
    color: var(--text-dim);
    line-height: 1.5;
  }

  /* ── FOOTER ── */
  footer {
    border-top: 1px solid var(--line);
    padding: 5vh 0;
    text-align: center;
  }

  footer p {
    font-size: .7rem;
    letter-spacing: .2em;
    text-transform: uppercase;
    color: var(--text-dim);
  }

  footer .gold-mark {
    color: var(--gold);
  }

  /* ── SCROLL REVEAL ── */
  .reveal {
    opacity: 0;
    transform: translateY(40px);
    transition: opacity .8s ease, transform .8s ease;
  }

  .reveal.visible {
    opacity: 1;
    transform: none;
  }

  /* ── RESPONSIVE ── */
  @media (max-width: 900px) {
    .intro-grid { grid-template-columns: 1fr; gap: 3rem; }
    .formats-grid { grid-template-columns: 1fr; max-width: 480px; margin: 0 auto; }
    .result-grid { grid-template-columns: 1fr; gap: 3rem; }
    .insight-block { grid-template-columns: 50px 1fr; gap: 1.5rem; }
  }

  @media (max-width: 600px) {
    .hero { padding: 0 5vw 8vh; }
    .container { padding: 0 5vw; }
    .hero-title { font-size: 5rem; }
  }
</style>
</head>
<body>

<!-- ════════════ HERO ════════════ -->
<section class="hero">
  <div class="hero-bg"></div>
  <div class="hero-line"></div>
  <div class="hero-meta">
    <span>Кейс-стади</span>
    <strong>Motion Design</strong>
  </div>
  <p class="hero-tag">Визуализатор · Музыкальная индустрия · UI/Motion</p>
  <h1 class="hero-title">
    BEAT
    <span>VISUALIZER</span>
  </h1>
  <p class="hero-subtitle">
    Разработка универсальной системы визуализации для продюсеров,
    масштабируемой под три формата: 16:9, 9:16 и 1:1.
  </p>
  <div class="scroll-hint">Листайте вниз</div>
</section>

<!-- ════════════ OVERVIEW ════════════ -->
<section class="section-intro">
  <div class="container">
    <div class="intro-grid reveal">
      <div>
        <p class="section-label">Обзор проекта</p>
        <h2 class="intro-heading">
          Фреймворк<br>
          <em>универсального</em><br>
          визуализатора
        </h2>
      </div>
      <div class="intro-body">
        <p>
          Цель проекта — спроектировать многоплатформенную систему визуализации, 
          которая масштабируется под три формата изображения (16:9, 9:16, 1:1), 
          сохраняя единый принцип «продукт прежде всего».
        </p>
        <p>
          Система должна быть достаточно универсальной, чтобы органично дополнять 
          любую обложку и любой стиль бита — не заглушая, а усиливая восприятие звука.
        </p>
        <p>
          Итогом стал «plug-and-play» движок: достаточно сменить обложку и обновить 
          текстовые поля — и визуализатор мгновенно адаптируется под новое настроение, 
          превращая аудиофайл в брендированный профессиональный продукт для 
          любой социальной экосистемы.
        </p>
      </div>
    </div>
  </div>
</section>

<!-- ════════════ VIDEO FORMATS ════════════ -->
<section class="section-formats">
  <div class="container">
    <div class="formats-header reveal">
      <p class="section-label">Три формата</p>
      <h2>Адаптивная раскладка<br>под <em>каждую платформу</em></h2>
    </div>
    <div class="formats-grid">
      <!-- 16:9 -->
      <div class="format-card reveal">
        <div class="format-label">
          <h3>Широкий</h3>
          <span class="ratio">16:9</span>
        </div>
        <p class="format-platform">YouTube · Desktop</p>
        <div class="video-wrap ratio-16-9">
          <iframe src="https://player.vimeo.com/video/1086842942?autoplay=0&title=0&byline=0&portrait=0" 
            allow="autoplay; fullscreen" allowfullscreen loading="lazy"></iframe>
        </div>
        <p class="format-desc">
          «Широкоэкранный кинематограф» — метаданные по углам, 
          центральная обложка занимает главенствующее положение.
        </p>
      </div>
      <!-- 9:16 -->
      <div class="format-card reveal" style="transition-delay:.1s">
        <div class="format-label">
          <h3>Вертикаль</h3>
          <span class="ratio">9:16</span>
        </div>
        <p class="format-platform">TikTok · Reels · Shorts</p>
        <div class="video-wrap ratio-9-16">
          <iframe src="https://player.vimeo.com/video/1183106565?autoplay=0&title=0&byline=0&portrait=0" 
            allow="autoplay; fullscreen" allowfullscreen loading="lazy"></iframe>
        </div>
        <p class="format-desc">
          Оптимизирован под «вертикальную безопасную зону» — элементы 
          остаются видимыми несмотря на UI-оверлеи платформ.
        </p>
      </div>
      <!-- 1:1 -->
      <div class="format-card reveal" style="transition-delay:.2s">
        <div class="format-label">
          <h3>Квадрат</h3>
          <span class="ratio">1:1</span>
        </div>
        <p class="format-platform">Instagram Feed</p>
        <div class="video-wrap ratio-1-1">
          <iframe src="https://player.vimeo.com/video/1183109367?autoplay=0&title=0&byline=0&portrait=0" 
            allow="autoplay; fullscreen" allowfullscreen loading="lazy"></iframe>
        </div>
        <p class="format-desc">
          Сбалансированная центрированная композиция: обложка-якорь, 
          метаданные обрамляют квадрат.
        </p>
      </div>
    </div>
  </div>
</section>

<!-- ════════════ INSIGHTS ════════════ -->
<section class="section-insights">
  <div class="container">
    <div class="insights-header reveal">
      <p class="section-label">Дизайн-процесс</p>
      <h2>Пять ключевых<br><em>решений</em></h2>
    </div>

    <!-- 1 -->
    <div class="insight-block reveal">
      <div class="insight-num">01</div>
      <div class="insight-content">
        <h3>Информация vs. Погружение</h3>
        <p>
          Главная сложность — плотность информации. Профессиональный визуализатор бита 
          не просто красивое видео; это инструмент продаж. Он должен вмещать ключ, темп, 
          аранжировку трека, QR-код, призыв к действию «Subscribe» и кредиты.
        </p>
        <p>
          Задача дизайна — не дать техническим элементам перегрузить экран 
          и не отвлечь слушателя от музыки.
        </p>
        <div class="tag-list">
          <span class="tag">Иерархия информации</span>
          <span class="tag">UX-анализ</span>
          <span class="tag">Инструмент продаж</span>
        </div>
      </div>
    </div>

    <!-- 2 -->
    <div class="insight-block reveal">
      <div class="insight-num">02</div>
      <div class="insight-content">
        <h3>Адаптивная система раскладок</h3>
        <p>
          Чтобы визуализатор ощущался нативным на каждой платформе, 
          была разработана система адаптивных раскладок для трёх форматов: 
          горизонталь для YouTube, вертикаль для TikTok с учётом безопасных зон, 
          и сбалансированный квадрат для Instagram.
        </p>
        <div class="tag-list">
          <span class="tag">Responsive Layout</span>
          <span class="tag">16:9 / 9:16 / 1:1</span>
          <span class="tag">Платформенная нативность</span>
        </div>
      </div>
    </div>

    <!-- 3 -->
    <div class="insight-block reveal">
      <div class="insight-num">03</div>
      <div class="insight-content">
        <h3>Модульный дизайн информации</h3>
        <p>
          Динамическая оверлейная система делает визуализатор «универсальным». 
          Стилизованный QR-код в углу обеспечивает мгновенную покупку без лишних 
          шагов. Чистые гротески высокой читаемости гарантируют, что ключ и BPM 
          выглядят органично — будь то Lo-fi или Aggressive Trap.
        </p>
        <p>
          Анимированный CTA «Subscribe» привлекает взгляд тонким движением, 
          не разрушая поток восприятия.
        </p>
        <div class="tag-list">
          <span class="tag">Модульность</span>
          <span class="tag">QR-интеграция</span>
          <span class="tag">Типографика</span>
          <span class="tag">Micro-animation</span>
        </div>
      </div>
    </div>

    <!-- 4 -->
    <div class="insight-block reveal">
      <div class="insight-num">04</div>
      <div class="insight-content">
        <h3>Реактивный минимализм</h3>
        <p>
          Баланс «крутого» и «ненавязчивого» достигается через принцип 
          <em>Reactive Minimalism</em>: вместо хаотичной волны — мягкое свечение 
          и пульсация, реагирующие на кик и снэр.
        </p>
        <p>
          Техника «размытого фонового слоя» — обложка масштабируется, 
          применяется тяжёлый Gaussian blur и тёмный оверлей — автоматически 
          формирует цельную цветовую палитру для любого бита. Текстуры зерна 
          и «пыли» добавляют тактильное, высококачественное ощущение 
          без тяжёлого рендеринга.
        </p>
        <div class="tag-list">
          <span class="tag">Gaussian Blur</span>
          <span class="tag">Film Grain</span>
          <span class="tag">Аудиореактивность</span>
        </div>
      </div>
    </div>

    <!-- 5 -->
    <div class="insight-block reveal">
      <div class="insight-num">05</div>
      <div class="insight-content">
        <h3>Plug-and-Play движок</h3>
        <p>
          Финальный фреймворк действует как универсальный движок: 
          смена обложки и обновление текстовых полей мгновенно адаптируют 
          визуализатор под новое настроение. Бит превращается из простого 
          аудиофайла в брендированный профессиональный продукт, 
          готовый к публикации в любой социальной экосистеме.
        </p>
        <div class="tag-list">
          <span class="tag">Масштабируемость</span>
          <span class="tag">Брендинг</span>
          <span class="tag">Автоматизация</span>
        </div>
      </div>
    </div>
  </div>
</section>

<!-- ════════════ PULLQUOTE ════════════ -->
<div class="pullquote-wrap">
  <div class="pullquote reveal">
    <blockquote>
      Хороший моушн-дизайн в музыкальной индустрии должен 
      <strong>ощущаться</strong>, а не <strong>бросаться в глаза</strong>.
      Визуализатор служит биту — а не конкурирует с ним.
    </blockquote>
    <cite>Ключевой вывод проекта</cite>
  </div>
</div>

<!-- ════════════ RESULT ════════════ -->
<section class="section-result">
  <div class="container">
    <div class="result-grid">
      <div class="result-text reveal">
        <p class="section-label">Результат</p>
        <h2>Продукт, который<br><em>работает</em> на музыку</h2>
        <p>
          Правильная иерархия приоритетов — Ссылка &gt; Метаданные &gt; Арт — 
          определяет итоговую ценность визуализатора как конверсионного инструмента.
        </p>
        <p>
          Система доказала свою состоятельность именно в универсальности: 
          один фреймворк покрывает все платформы, все стили, 
          любую обложку — без потери качества или идентичности.
        </p>
      </div>
      <div class="result-stats reveal" style="transition-delay:.15s">
        <div class="stat-row">
          <div class="stat-icon">3</div>
          <div class="stat-info">
            <h4>Формата в одном фреймворке</h4>
            <p>16:9 · 9:16 · 1:1 — нативно для каждой платформы</p>
          </div>
        </div>
        <div class="stat-row">
          <div class="stat-icon">∞</div>
          <div class="stat-info">
            <h4>Адаптируется под любой стиль</h4>
            <p>От Lo-fi до Hard Trap — автоматическая палитра из обложки</p>
          </div>
        </div>
        <div class="stat-row">
          <div class="stat-icon">QR</div>
          <div class="stat-info">
            <h4>Прямая конверсия</h4>
            <p>Встроенный QR-код — мгновенная покупка без лишних шагов</p>
          </div>
        </div>
        <div class="stat-row">
          <div class="stat-icon">↔</div>
          <div class="stat-info">
            <h4>Plug-and-Play система</h4>
            <p>Смена обложки и текста — визуализатор готов к публикации</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</section>

<!-- ════════════ FOOTER ════════════ -->
<footer>
  <div class="container">
    <p>
      <span class="gold-mark">Beat Visualizer</span> &nbsp;·&nbsp; 
      Кейс-стади по Motion Design &nbsp;·&nbsp; 
      Universal Visualizer Framework
    </p>
  </div>
</footer>

<script>
  // Scroll reveal
  const reveals = document.querySelectorAll('.reveal');
  const observer = new IntersectionObserver((entries) => {
    entries.forEach(e => {
      if (e.isIntersecting) {
        e.target.classList.add('visible');
        observer.unobserve(e.target);
      }
    });
  }, { threshold: 0.12 });
  reveals.forEach(el => observer.observe(el));
</script>
</body>
</html>
