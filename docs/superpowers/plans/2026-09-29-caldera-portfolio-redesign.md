# Rediseño Portfolio estilo Caldera — Plan de Implementación

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rediseñar `index.html` con el lenguaje visual "Caldera" usando los colores propios del sitio, sumar animación con vida, y convertir la sección de Proyectos en la estrella con los proyectos reales del GitHub.

**Architecture:** Un único `index.html` estático (HTML + CSS + JS vanilla, sin build). Se reemplaza el `<style>` completo por un CSS con tokens nuevos, se reestructura el `<body>` sección por sección preservando el contenido multi-idioma (`data-es/en/jp`) y la foto en base64, y se amplía el `<script>` con nuevas interacciones (filtro de proyectos, contador, botones magnéticos, parallax) manteniendo el switcher de idioma, el scroll-reveal y el cursor glow existentes.

**Tech Stack:** HTML5, CSS3 (custom properties, grid, clip-path, radial-gradient para halftone), JavaScript vanilla (IntersectionObserver), Google Fonts (Bebas Neue + DM Sans, ya cargadas).

## Global Constraints

- **Un solo archivo `index.html`.** Sin framework, sin bundler, sin build step, sin dependencias JS externas. Deploy en Netlify.
- **Preservar el contenido multi-idioma ES/EN/JP** vía atributos `data-es`/`data-en`/`data-jp`. Todo texto nuevo DEBE tener los 3.
- **Preservar la foto en base64** (elemento `<img>` dentro de `.photo-frame`, ~186KB en una sola línea). NUNCA reescribir ni borrar ese `src`.
- **Preservar links externos exactos:** GitHub `github.com/santinovargasdb/<repo>`, LinkedIn `https://www.linkedin.com/in/santino-vargas-di-buono-7b77b2378`, mail `mailto:Santivargasdb@gmail.com`.
- **Sin sombras:** cero `box-shadow` en el CSS final. Jerarquía por color + radio, hovers por `transform`/`border`/color.
- **Radios:** cards `40px`, píldoras `800px`, inputs `100px`, chico `16px`.
- **Tipografía:** Bebas Neue display (lh 0.94, tracking +0.02em); DM Sans **500** siempre para cuerpo (nunca 300/400/700 en body).
- **Accesibilidad:** toda animación bajo `@media (prefers-reduced-motion: no-preference)`; con reduce-motion el sitio queda estático y usable.
- **Paleta (tokens):** `--canvas #ede6d6` · `--surface #f5f0e8` · `--surface-2 #e4dbc8` · `--fire #e84e1b` · `--fire2 #ff7a3d` · `--yellow #f5c800` · `--cyan #00c2cc` · `--cyan2 #5ee8f0` · `--ink #1a1612` · `--muted #7a6f60` · `--chalk #ffffff`.
- **Rama de trabajo:** `redesign/caldera-style` (ya creada). Commits frecuentes por tarea.

**Verificación local (se usa en todas las tareas):**
```bash
cd "C:/Users/accsoc/portfolio/portfolio-web"
python -m http.server 8000
# abrir http://localhost:8000 en el navegador
```
Chequeo de sombras (debe dar 0 al final):
```bash
grep -c "box-shadow" index.html
```

---

### Task 1: Fundaciones CSS (tokens, base, tipografía, utilidades)

Reemplaza el bloque `:root` + base del `<style>` con los tokens nuevos, contenedor centrado, escala tipográfica y utilidades (divisor punteado, halftone, reduce-motion). Deja el resto del CSS existente por ahora (se reescribe en tareas siguientes); puede verse "roto" a mitad — es esperado.

**Files:**
- Modify: `index.html` (bloque `<style>`, líneas ~9-16 `:root`+`body`)

**Interfaces:**
- Produces: variables CSS `--canvas --surface --surface-2 --fire --fire2 --yellow --cyan --cyan2 --ink --muted --chalk`; radios `--r-card:40px --r-pill:800px --r-input:100px --r-sm:16px`; clases utilitarias `.container` (max-width 1280px, margin auto, padding lateral), `.dotted` (border punteado), `.halftone` (patrón de puntos como background-image).

- [ ] **Step 1: Reemplazar `:root` y `body`**

En `index.html`, reemplazar el `:root{...}` y la regla `body{...}` actuales por:

```css
:root{
  --canvas:#ede6d6;--surface:#f5f0e8;--surface-2:#e4dbc8;
  --ink:#1a1612;--muted:#7a6f60;--chalk:#ffffff;
  --fire:#e84e1b;--fire2:#ff7a3d;--yellow:#f5c800;
  --cyan:#00c2cc;--cyan2:#5ee8f0;
  --border:rgba(26,22,18,0.12);--dot:rgba(26,22,18,0.32);
  --font-display:"Bebas Neue",sans-serif;--font-body:"DM Sans",sans-serif;
  --r-card:40px;--r-pill:800px;--r-input:100px;--r-sm:16px;
  --maxw:1280px;--gap-sec:80px;--pad-card:40px;
}
body{background:var(--canvas);color:var(--ink);font-family:var(--font-body);font-weight:500;font-size:16px;line-height:1.55;overflow-x:hidden;-webkit-font-smoothing:antialiased}
.container{max-width:var(--maxw);margin:0 auto;width:100%}
/* dotted divider utility */
.dotted{border:0;border-top:1.5px dotted var(--dot)}
/* halftone dot pattern (orange dots) */
.halftone{background-image:radial-gradient(var(--fire) 22%,transparent 23%);background-size:14px 14px}
h1,h2,h3,.display{font-family:var(--font-display);letter-spacing:0.02em;line-height:0.94;font-weight:400}
a{color:inherit}
```

- [ ] **Step 2: Agregar guard de reduce-motion al final del `<style>`**

Antes de `</style>`, agregar:

```css
@media (prefers-reduced-motion: reduce){
  *,*::before,*::after{animation-duration:0.001ms!important;animation-iteration-count:1!important;transition-duration:0.001ms!important;scroll-behavior:auto!important}
}
```

- [ ] **Step 3: Verificar en navegador**

Levantar `python -m http.server 8000`, abrir. Expected: el fondo pasa a crema-medio `#ede6d6`, sin errores en consola (F12). El layout se ve a medio hacer (esperado). Confirmar que la foto del hero sigue apareciendo.

- [ ] **Step 4: Commit**

```bash
git add index.html && git commit -m "feat(redesign): tokens, base y utilidades CSS estilo Caldera"
```

---

### Task 2: Navegación (píldora limestone + iconos de redes)

Reestilar el nav: items dentro de un contenedor píldora, idiomas como píldoras, y agregar iconos de redes (LinkedIn/GitHub) a la derecha. Mantener el switcher de idioma funcionando.

**Files:**
- Modify: `index.html` (CSS de `nav` líneas ~19-28; HTML `<nav>` líneas ~317-330)

**Interfaces:**
- Consumes: tokens de Task 1.
- Produces: markup de nav con `.nav-pill` (contenedor de links) y `.nav-social` (iconos). Los `.lang-btn` conservan su clase y su handler JS existente.

- [ ] **Step 1: Reemplazar el CSS de nav**

```css
nav{position:sticky;top:0;z-index:100;display:flex;align-items:center;justify-content:space-between;gap:16px;padding:16px 40px;background:color-mix(in srgb,var(--canvas) 88%,transparent);backdrop-filter:blur(10px);border-bottom:1.5px dotted var(--dot)}
.nav-logo{font-family:var(--font-body);font-size:15px;font-weight:600;letter-spacing:0.02em;color:var(--ink);white-space:nowrap}
.nav-logo span{color:var(--fire)}
.nav-pill{display:flex;gap:4px;align-items:center;background:var(--surface);border-radius:var(--r-pill);padding:6px 10px}
.nav-pill a{color:var(--ink);text-decoration:none;font-size:14px;font-weight:500;padding:8px 14px;border-radius:var(--r-pill);transition:background 0.18s,color 0.18s}
.nav-pill a:hover{background:var(--ink);color:var(--surface)}
.nav-right{display:flex;align-items:center;gap:10px}
.nav-social{display:flex;gap:6px}
.nav-social a{display:grid;place-items:center;width:34px;height:34px;border-radius:var(--r-pill);border:1.5px solid var(--border);color:var(--ink);text-decoration:none;font-size:15px;transition:all 0.18s}
.nav-social a:hover{background:var(--fire);border-color:var(--fire);color:#fff;transform:translateY(-2px)}
.lang-toggle{display:flex;gap:4px}
.lang-btn{background:none;border:1.5px solid var(--border);color:var(--muted);font-size:11px;font-weight:700;padding:6px 11px;border-radius:var(--r-pill);cursor:pointer;font-family:var(--font-body);letter-spacing:0.08em;transition:all 0.18s}
.lang-btn.active{background:var(--ink);color:var(--surface);border-color:var(--ink)}
.lang-btn:hover:not(.active){border-color:var(--ink);color:var(--ink)}
```

- [ ] **Step 2: Reemplazar el HTML de `<nav>`**

Mantener el `<ul class="nav-links">` → convertir a `.nav-pill`, agregar `.nav-right` con social + idiomas. Conservar los `data-*` de cada link:

```html
<nav>
  <div class="nav-logo">Santino Vargas Di Buono<span>.</span></div>
  <div class="nav-pill">
    <a href="#sobre-mi" data-es="Sobre mi" data-en="About" data-jp="自己紹介">Sobre mi</a>
    <a href="#skills" data-es="Skills" data-en="Skills" data-jp="スキル">Skills</a>
    <a href="#proyectos" data-es="Proyectos" data-en="Projects" data-jp="プロジェクト">Proyectos</a>
    <a href="#contacto" data-es="Contacto" data-en="Contact" data-jp="連絡">Contacto</a>
  </div>
  <div class="nav-right">
    <div class="nav-social">
      <a href="https://github.com/santinovargasdb" target="_blank" rel="noopener" aria-label="GitHub">&#128025;</a>
      <a href="https://www.linkedin.com/in/santino-vargas-di-buono-7b77b2378" target="_blank" rel="noopener" aria-label="LinkedIn">in</a>
    </div>
    <div class="lang-toggle">
      <button class="lang-btn active">ES</button>
      <button class="lang-btn">EN</button>
      <button class="lang-btn">JP</button>
    </div>
  </div>
</nav>
```

- [ ] **Step 3: Verificar**

Recargar. Expected: nav con caja píldora clara para los links, hover invierte a oscuro, iconos redondos a la derecha, botones ES/EN/JP en píldora. Click en EN/JP cambia todo el texto (incluye nav). Links de redes abren en pestaña nueva.

- [ ] **Step 4: Commit**

```bash
git add index.html && git commit -m "feat(redesign): nav en pildora con iconos de redes"
```

---

### Task 3: Hero (nombre gigante + bloque halftone con foto + CTAs píldora)

Reestructurar el hero: nombre a escala arquitectónica, tag/rol/desc, 2 CTAs píldora, y a la derecha el bloque halftone de 40px con la foto en tratamiento duotono + puntos + chips como píldoras. **Preservar el `<img>` en base64.**

**Files:**
- Modify: `index.html` (CSS hero líneas ~30-79; HTML hero líneas ~332-371)

**Interfaces:**
- Consumes: tokens Task 1; `.container`.
- Produces: `.btn-fire` y `.btn-outline` (píldoras, reutilizadas en contacto); `.photo-halftone` (contenedor de la foto); clase `.mag` (marca botones magnéticos para JS de Task 10).

- [ ] **Step 1: Reemplazar el CSS del hero**

```css
.hero{max-width:var(--maxw);margin:0 auto;min-height:88vh;display:grid;grid-template-columns:1fr 440px;align-items:center;gap:48px;padding:64px 40px;position:relative}
.hero-left{position:relative;z-index:2}
.hero-tag{display:inline-flex;align-items:center;gap:8px;background:var(--surface);border:1.5px solid var(--border);color:var(--ink);font-size:12px;font-weight:600;letter-spacing:0.08em;text-transform:uppercase;padding:7px 16px;border-radius:var(--r-pill);margin-bottom:28px}
.hero-tag::before{content:"";width:7px;height:7px;background:var(--fire);border-radius:50%;animation:pulse 1.5s ease-in-out infinite}
@keyframes pulse{0%,100%{opacity:1;transform:scale(1)}50%{opacity:0.35;transform:scale(0.6)}}
.hero-name{font-family:var(--font-display);font-size:clamp(72px,13vw,150px);line-height:0.9;letter-spacing:0.02em;color:var(--ink);margin-bottom:16px}
.hero-name .fire{color:var(--fire)}
.hero-role{font-size:14px;font-weight:600;color:var(--muted);letter-spacing:0.08em;text-transform:uppercase;margin-bottom:22px;display:flex;align-items:center;gap:12px}
.hero-role::before{content:"";width:36px;height:2px;background:var(--fire);flex-shrink:0}
.hero-desc{max-width:480px;color:var(--muted);font-size:17px;line-height:1.7;margin-bottom:36px}
.hero-desc strong{color:var(--ink);font-weight:600}
.hero-ctas{display:flex;gap:12px;flex-wrap:wrap}
.btn-fire{display:inline-flex;align-items:center;gap:8px;background:var(--fire);color:#fff;font-weight:600;font-size:16px;padding:14px 28px;border-radius:var(--r-pill);text-decoration:none;border:none;cursor:pointer;font-family:var(--font-body);transition:transform 0.2s,background 0.2s}
.btn-fire:hover{background:var(--ink)}
.btn-outline{display:inline-flex;align-items:center;gap:8px;background:transparent;color:var(--ink);font-weight:600;font-size:16px;padding:14px 28px;border-radius:var(--r-pill);text-decoration:none;border:1.5px solid var(--ink);cursor:pointer;font-family:var(--font-body);transition:all 0.2s}
.btn-outline:hover{background:var(--ink);color:var(--surface)}
/* right: halftone photo block */
.hero-right{position:relative;z-index:2;display:flex;flex-direction:column;gap:14px}
.photo-halftone{position:relative;border-radius:var(--r-card);overflow:hidden;background:linear-gradient(150deg,var(--cyan) 0%,var(--fire) 100%);aspect-ratio:4/5}
.photo-halftone img{width:100%;height:100%;object-fit:cover;object-position:center top;mix-blend-mode:luminosity;opacity:0.92}
.photo-halftone::after{content:"";position:absolute;inset:0;background-image:radial-gradient(rgba(232,78,27,0.9) 20%,transparent 21%);background-size:12px 12px;mix-blend-mode:normal;opacity:0.5;-webkit-mask-image:linear-gradient(135deg,#000 0%,transparent 55%);mask-image:linear-gradient(135deg,#000 0%,transparent 55%);animation:halftone 8s ease-in-out infinite}
@keyframes halftone{0%,100%{background-position:0 0}50%{background-position:6px 6px}}
.photo-chips{display:flex;flex-direction:column;gap:8px}
.photo-chip{display:flex;align-items:center;gap:10px;background:var(--surface);border:1.5px solid var(--border);border-radius:var(--r-pill);padding:10px 18px;font-size:13px;font-weight:600;color:var(--ink)}
.photo-chip-icon{font-size:15px;flex-shrink:0}
.blob{display:none} /* remove old blurred blobs */
```

- [ ] **Step 2: Reemplazar el HTML del hero (preservando el `<img>`)**

Mantener EXACTO el `<img src="data:image/...">` que está dentro de `.photo-frame`. Nueva estructura:

```html
<section class="hero">
  <div class="hero-left">
    <div class="hero-tag mag" data-es="Disponible ahora" data-en="Available now" data-jp="今すぐ対応可能">Disponible ahora</div>
    <div class="hero-name">SANTINO<br>VARGAS<span class="fire">.</span></div>
    <p class="hero-role" data-es="Desarrollador web &amp; tech &middot; Buenos Aires" data-en="Web &amp; Tech Developer &middot; Buenos Aires" data-jp="ウェブ開発者 &middot; ブエノスアイレス">Desarrollador web &amp; tech &middot; Buenos Aires</p>
    <p class="hero-desc"
      data-es='Estudiante de <strong>Tecnicatura en Informática</strong> en Buenos Aires. Frontend, IoT y automatización. Hablo <strong>3 idiomas</strong>, lidero equipos y entrego resultados. Buscando mi primera oportunidad profesional.'
      data-en='IT student specializing in <strong>Computer Science</strong> in Buenos Aires. Frontend, IoT and automation. I speak <strong>3 languages</strong>, lead teams and deliver results. Looking for my first professional opportunity.'
      data-jp='ブエノスアイレスで<strong>情報科学</strong>を専攻する学生。フロントエンド、IoT、自動化。<strong>3言語</strong>を話し、チームをリードし、成果を出す。初めてのプロの機会を探しています。'>
      Estudiante de <strong>Tecnicatura en Informática</strong> en Buenos Aires. Frontend, IoT y automatizacion. Hablo <strong>3 idiomas</strong>, lidero equipos y entrego resultados. Buscando mi primera oportunidad profesional.
    </p>
    <div class="hero-ctas">
      <a href="#proyectos" class="btn-fire mag" data-es="Ver proyectos →" data-en="View projects →" data-jp="プロジェクトを見る →">Ver proyectos →</a>
      <a href="#contacto" class="btn-outline mag" data-es="Hablemos" data-en="Let's talk" data-jp="話しましょう">Hablemos</a>
    </div>
  </div>
  <div class="hero-right">
    <div class="photo-halftone photo-frame">
      <!-- PRESERVAR el <img src="data:image/..."> existente aquí, sin cambios -->
    </div>
    <div class="photo-chips">
      <div class="photo-chip" data-es="🎓 Técnico en Informática" data-en="🎓 IT Technician" data-jp="🎓 情報技術者"><span class="photo-chip-icon">&#127891;</span> Tecnico en Informatica</div>
      <div class="photo-chip" data-es="⚡ Certificado en Metodologías Ágiles" data-en="⚡ Certified in Agile" data-jp="⚡ アジャイル認定"><span class="photo-chip-icon">&#9889;</span> Certificado en Metodologias Agiles</div>
      <div class="photo-chip" data-es="🌍 Inglés B2 · Japonés N5 · Español nativo" data-en="🌍 English B2 · Japanese N5 · Native Spanish" data-jp="🌍 英語B2・日本語N5・スペイン語母語"><span class="photo-chip-icon">&#127759;</span> Ingles B2 &middot; Japones N5 &middot; Espanol nativo</div>
    </div>
  </div>
</section>
```

**Nota de implementación:** copiar el nodo `<img ...>` original tal cual (con su `src` base64 completo) dentro de `.photo-halftone`. Eliminar los `<div class="hero-slash">`, `.hero-slash-2`, `.blob-1`, `.blob-2`.

- [ ] **Step 3: Verificar**

Recargar. Expected: nombre gigante "SANTINO VARGAS.", tag píldora con dot pulsante, 2 botones píldora (naranja + outline), a la derecha la foto con tratamiento duotono cyan→naranja + puntos halftone en la esquina superior izquierda, chips píldora debajo. La foto se ve (no rota). Cambiar idioma actualiza todo.

- [ ] **Step 4: Commit**

```bash
git add index.html && git commit -m "feat(redesign): hero con nombre gigante y bloque halftone"
```

---

### Task 4: Ticker + secciones base

Reestilar la marquesina y las reglas comunes de `section`, `.section-label`, `.section-title` (usadas por Sobre mí, Skills, Proyectos).

**Files:**
- Modify: `index.html` (CSS ticker ~82-89 y sections ~91-94)

**Interfaces:**
- Consumes: tokens Task 1.
- Produces: `.section-label`, `.section-title` (Bebas grande), envoltura `section > .container`.

- [ ] **Step 1: Reemplazar CSS de ticker y sections**

```css
.ticker-wrap{background:var(--ink);overflow:hidden;padding:14px 0;border-top:1.5px dotted rgba(245,240,232,0.25);border-bottom:1.5px dotted rgba(245,240,232,0.25)}
.ticker{display:flex;animation:ticker 24s linear infinite;white-space:nowrap;width:max-content}
@keyframes ticker{from{transform:translateX(0)}to{transform:translateX(-50%)}}
.ticker-item{display:inline-flex;align-items:center;gap:14px;padding:0 28px;font-family:var(--font-display);font-size:18px;letter-spacing:2px;color:rgba(245,240,232,0.5)}
.ticker-item .dot{width:6px;height:6px;border-radius:50%}
.ticker-item:nth-child(3n+1) .dot{background:var(--fire)}
.ticker-item:nth-child(3n+2) .dot{background:var(--yellow)}
.ticker-item:nth-child(3n) .dot{background:var(--cyan)}
section{padding:var(--gap-sec) 40px;border:0}
section > .container{max-width:var(--maxw);margin:0 auto}
.section-label{font-size:12px;font-weight:700;letter-spacing:0.16em;text-transform:uppercase;color:var(--fire);margin-bottom:12px}
.section-title{font-family:var(--font-display);font-size:clamp(44px,7vw,90px);letter-spacing:0.02em;line-height:0.94;color:var(--ink);margin-bottom:48px}
```

- [ ] **Step 2: Envolver contenido de cada `<section>` en `.container`**

Para `#sobre-mi`, `#skills`, `#proyectos`, `#contacto`: agregar `<div class="container">` justo después de la etiqueta `<section...>` y cerrarlo antes de `</section>`. (Se hace junto con la reestructuración de cada sección en las tareas 5-8; en esta tarea solo el ticker y las reglas base.)

- [ ] **Step 3: Verificar**

Recargar. Expected: barra ticker oscura con bordes punteados e items Bebas más grandes; los títulos de sección (Sobre mí / Skills / etc.) ahora son gigantes en Bebas. El contenido puede no estar aún centrado en 1280 (se arregla al envolver en `.container` por sección).

- [ ] **Step 4: Commit**

```bash
git add index.html && git commit -m "feat(redesign): ticker y titulos de seccion a escala Caldera"
```

---

### Task 5: Sobre mí (cards planas + pills, stats chicas planas)

Reestilar la sección Sobre mí: texto editorial, identity cards planas 40px con acento de color, about-pills en píldora, stats chicas planas. Sin franja de stats grande.

**Files:**
- Modify: `index.html` (CSS about ~104-129; HTML `#sobre-mi` ~398-445, envolver en `.container`)

**Interfaces:**
- Consumes: tokens; `.section-label`, `.section-title`.
- Produces: `.pill`, `.identity-card`, `.about-stat` (versiones planas).

- [ ] **Step 1: Reemplazar CSS de about**

```css
.about-layout{display:grid;grid-template-columns:1.2fr 1fr;gap:48px;align-items:start}
.about-text p{color:var(--muted);line-height:1.8;margin-bottom:16px;font-size:16px}
.about-text strong{color:var(--ink);font-weight:600}
.about-pills{display:flex;flex-wrap:wrap;gap:8px;margin-top:24px}
.pill{font-size:13px;font-weight:600;padding:7px 16px;border-radius:var(--r-pill);border:1.5px solid var(--border);color:var(--ink);background:var(--surface)}
.pill.fire{background:var(--fire);color:#fff;border-color:var(--fire)}
.pill.yellow{background:var(--yellow);color:var(--ink);border-color:var(--yellow)}
.pill.cyan{background:var(--cyan);color:var(--ink);border-color:var(--cyan)}
.about-stats{display:grid;grid-template-columns:repeat(3,1fr);gap:12px;margin-top:28px}
.about-stat{border-radius:var(--r-sm);padding:20px 16px;text-align:center}
.about-stat.fire{background:var(--fire);color:#fff}
.about-stat.yellow{background:var(--yellow);color:var(--ink)}
.about-stat.cyan{background:var(--cyan);color:var(--ink)}
.about-stat .n{font-family:var(--font-display);font-size:40px;letter-spacing:1px;line-height:1}
.about-stat .l{font-size:10px;font-weight:700;text-transform:uppercase;letter-spacing:0.08em;opacity:0.85;margin-top:6px}
.about-right{display:flex;flex-direction:column;gap:12px}
.identity-card{background:var(--surface);border:1.5px solid var(--border);border-left:4px solid transparent;border-radius:var(--r-sm);padding:18px 22px;display:flex;align-items:center;gap:16px;transition:transform 0.2s,border-color 0.2s}
.identity-card:hover{transform:translateX(6px)}
.identity-card.c-fire{border-left-color:var(--fire)}
.identity-card.c-yellow{border-left-color:var(--yellow)}
.identity-card.c-cyan{border-left-color:var(--cyan)}
.identity-card-icon{font-size:24px;width:30px;text-align:center;flex-shrink:0}
.identity-card-title{font-weight:700;font-size:15px;color:var(--ink)}
.identity-card-sub{font-size:12px;color:var(--muted);margin-top:2px}
```

- [ ] **Step 2: Envolver `#sobre-mi` en `.container`**

Estructura: `<section id="sobre-mi"><div class="container"> ...(section-label, section-title, about-layout)... </div></section>`. Conservar TODO el contenido `data-*` existente de los párrafos, pills, stats e identity-cards (no reescribir textos). Mantener las clases `reveal`/`reveal-left`/`reveal-right`.

- [ ] **Step 3: Verificar**

Recargar. Expected: sección centrada (max 1280), texto legible, pills en píldora coloreadas, identity cards planas con borde izquierdo de color que se desplazan en hover, stats chicas planas. Cambio de idioma OK.

- [ ] **Step 4: Commit**

```bash
git add index.html && git commit -m "feat(redesign): sobre mi con cards planas y pills"
```

---

### Task 6: Skills (tira con divisores punteados)

Reestilar Skills: 4 columnas separadas por divisores punteados (sin bordes de grilla), encabezados Bebas con subrayado de color, skills fuertes como pills.

**Files:**
- Modify: `index.html` (CSS skills ~131-147; HTML `#skills` ~447-492, envolver en `.container`)

**Interfaces:**
- Consumes: tokens; `.section-title`.
- Produces: `.skills-strip`, `.skill-col`, `.skill-pill`.

- [ ] **Step 1: Reemplazar CSS de skills**

```css
.skills-strip{display:grid;grid-template-columns:repeat(4,1fr);gap:0}
.skill-col{padding:8px 28px;border-left:1.5px dotted var(--dot)}
.skill-col:first-child{border-left:0;padding-left:0}
.skill-col-head{font-family:var(--font-display);font-size:26px;letter-spacing:1px;color:var(--ink);margin-bottom:18px;padding-bottom:10px;border-bottom:3px solid transparent;display:inline-block}
.skill-col:nth-child(1) .skill-col-head{border-color:var(--fire)}
.skill-col:nth-child(2) .skill-col-head{border-color:var(--yellow)}
.skill-col:nth-child(3) .skill-col-head{border-color:var(--cyan)}
.skill-col:nth-child(4) .skill-col-head{border-color:var(--fire2)}
.skill-list{display:flex;flex-direction:column;gap:10px}
.skill-item{display:flex;align-items:center;gap:8px;font-size:14px;color:var(--muted);font-weight:500}
.skill-item::before{content:"";width:5px;height:5px;border-radius:50%;background:var(--muted);opacity:0.4;flex-shrink:0}
.skill-item.strong{align-self:flex-start;color:var(--ink);font-weight:600;background:var(--surface);border:1.5px solid var(--border);border-radius:var(--r-pill);padding:5px 14px}
.skill-item.strong::before{display:none}
```

- [ ] **Step 2: Actualizar HTML de `#skills`**

Envolver en `.container`; cambiar `<div class="skills-layout ...">` por `<div class="skills-strip reveal">`. Conservar las 4 `.skill-col` con sus items y `data-*` existentes.

- [ ] **Step 3: Verificar**

Recargar. Expected: 4 columnas separadas por líneas punteadas verticales, encabezados Bebas subrayados en color, skills "fuertes" como píldoras. Cambio de idioma OK.

- [ ] **Step 4: Commit**

```bash
git add index.html && git commit -m "feat(redesign): skills en tira con divisores punteados"
```

---

### Task 7: Proyectos (SECCIÓN ESTRELLA) — tiers + grid filtrable con 9 proyectos reales

Reconstruir Proyectos: Tier 1 AlToque (halftone), Tier 2 Smart Home (card oscura), Tier 3 grid filtrable de 7 cards con pills de categoría + contador. Datos reales del catálogo del spec.

**Files:**
- Modify: `index.html` (CSS projects ~149-202; HTML `#proyectos` ~494-601, envolver en `.container`)

**Interfaces:**
- Consumes: tokens; `.section-title`; `.section-label`.
- Produces: `.proj-hero` (tier1), `.proj-dark` (tier2), `.filter-bar`, `.filter-pill[data-filter]`, `.proj-grid`, `.proj-card[data-cat]`, `.proj-count` (nº contador para JS Task 10). Categorías: `fullstack`, `data`. Filtros: `all`, `fullstack`, `data`.

- [ ] **Step 1: CSS de la sección de proyectos**

```css
/* Tier 1 — insignia halftone */
.proj-hero{position:relative;border-radius:var(--r-card);overflow:hidden;background:linear-gradient(135deg,var(--cyan) 0%,var(--fire) 100%);color:#fff;padding:48px;margin-bottom:20px}
.proj-hero::after{content:"";position:absolute;inset:0;background-image:radial-gradient(rgba(255,255,255,0.6) 18%,transparent 19%);background-size:16px 16px;-webkit-mask-image:linear-gradient(200deg,#000,transparent 60%);mask-image:linear-gradient(200deg,#000,transparent 60%);opacity:0.4;pointer-events:none}
.proj-hero > *{position:relative;z-index:1}
.proj-hero .badge{display:inline-block;font-size:12px;font-weight:700;letter-spacing:0.08em;text-transform:uppercase;padding:5px 14px;border-radius:var(--r-pill);background:rgba(0,0,0,0.25);margin-bottom:18px}
.proj-hero h3{font-family:var(--font-display);font-size:clamp(40px,6vw,72px);line-height:0.94;margin-bottom:14px}
.proj-hero p{max-width:560px;font-size:16px;line-height:1.7;opacity:0.92;margin-bottom:22px}
/* Tier 2 — dark destacado */
.proj-dark{position:relative;background:var(--ink);color:var(--surface);border-radius:var(--r-card);padding:44px;overflow:hidden;margin-bottom:40px}
.proj-dark::before{content:"";position:absolute;top:0;left:0;right:0;height:4px;background:linear-gradient(90deg,var(--fire),var(--yellow),var(--cyan))}
.proj-dark .badge{display:inline-block;font-size:12px;font-weight:700;letter-spacing:0.08em;text-transform:uppercase;padding:5px 14px;border-radius:var(--r-pill);background:var(--yellow);color:var(--ink);margin-bottom:16px}
.proj-dark h3{font-family:var(--font-display);font-size:clamp(34px,5vw,56px);line-height:0.96;margin-bottom:14px}
.proj-dark p{max-width:640px;font-size:15px;line-height:1.7;color:rgba(245,240,232,0.7);margin-bottom:20px}
/* tags + links compartidos */
.tags{display:flex;flex-wrap:wrap;gap:7px;margin-bottom:8px}
.tag{font-size:12px;font-weight:600;padding:5px 12px;border-radius:var(--r-pill);background:var(--surface-2);color:var(--muted)}
.proj-hero .tag{background:rgba(255,255,255,0.18);color:#fff}
.proj-dark .tag{background:rgba(255,255,255,0.08);color:rgba(245,240,232,0.75)}
.links{display:flex;flex-wrap:wrap;gap:16px;margin-top:18px}
.plink{display:inline-flex;align-items:center;gap:6px;font-size:13px;font-weight:700;letter-spacing:0.04em;text-transform:uppercase;text-decoration:none;color:inherit;border-bottom:2px solid transparent;padding-bottom:2px;transition:border-color 0.2s}
.plink:hover{border-bottom-color:currentColor}
/* filter bar */
.filter-bar{display:flex;flex-wrap:wrap;gap:8px;margin:8px 0 24px}
.filter-pill{font-size:14px;font-weight:600;padding:8px 18px;border-radius:var(--r-pill);border:1.5px solid var(--border);background:transparent;color:var(--ink);cursor:pointer;font-family:var(--font-body);transition:all 0.18s}
.filter-pill.active{background:var(--ink);color:var(--surface);border-color:var(--ink)}
.filter-pill:hover:not(.active){border-color:var(--ink)}
/* grid */
.proj-count{font-family:var(--font-display);font-size:26px;color:var(--fire);margin-left:6px}
.proj-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:16px}
.proj-card{display:flex;flex-direction:column;background:var(--surface);border:1.5px solid var(--border);border-radius:var(--r-card);padding:28px;transition:transform 0.22s,border-color 0.22s}
.proj-card:hover{transform:translateY(-6px);border-color:var(--fire)}
.proj-card.hide{display:none}
.cat-tag{align-self:flex-start;font-size:11px;font-weight:700;letter-spacing:0.06em;text-transform:uppercase;padding:5px 12px;border-radius:var(--r-pill);background:var(--yellow);color:var(--ink);margin-bottom:14px}
.proj-card h4{font-family:var(--font-display);font-size:30px;letter-spacing:0.5px;line-height:1;color:var(--ink);margin-bottom:10px}
.proj-card .desc{font-size:14px;color:var(--muted);line-height:1.65;margin-bottom:16px}
.live-dot{display:inline-flex;align-items:center;gap:6px;font-size:11px;font-weight:700;text-transform:uppercase;letter-spacing:0.06em;color:var(--fire)}
.live-dot::before{content:"";width:7px;height:7px;border-radius:50%;background:var(--fire);animation:pulse 1.5s ease-in-out infinite}
.proj-card .links{margin-top:auto}
@media (max-width:1024px){.proj-grid{grid-template-columns:repeat(2,1fr)}}
```

- [ ] **Step 2: HTML — Tier 1 (AlToque) y Tier 2 (Smart Home)**

Dentro de `<section id="proyectos"><div class="container">`, tras `.section-label` + `.section-title`:

```html
<!-- TIER 1: insignia -->
<div class="proj-hero reveal">
  <span class="badge" data-es="Proyecto insignia · Full-stack" data-en="Flagship · Full-stack" data-jp="代表作 · フルスタック">Proyecto insignia &middot; Full-stack</span>
  <h3>ALTOQUE</h3>
  <p data-es="Marketplace que conecta personas con profesionales de oficios verificados —plomeros, electricistas, cerrajeros— para urgencias y trabajos agendados. Matching por geolocalización (PostGIS), pago híbrido con Mercado Pago e instalable como PWA." data-en="Marketplace connecting people with verified trade professionals —plumbers, electricians, locksmiths— for emergencies and scheduled jobs. Geolocation matching (PostGIS), hybrid Mercado Pago payments and installable as a PWA." data-jp="認証済みの職人（配管工・電気工・鍵屋）とユーザーをつなぐマーケットプレイス。位置情報マッチング（PostGIS）、Mercado Pagoハイブリッド決済、PWA対応。">Marketplace que conecta personas con profesionales de oficios verificados &mdash;plomeros, electricistas, cerrajeros&mdash; para urgencias y trabajos agendados. Matching por geolocalizacion (PostGIS), pago hibrido con Mercado Pago e instalable como PWA.</p>
  <div class="tags">
    <span class="tag">Next.js 15</span><span class="tag">TypeScript</span><span class="tag">Supabase</span><span class="tag">PostGIS</span><span class="tag">Mercado Pago</span><span class="tag">Tailwind v4</span><span class="tag">PWA</span>
  </div>
  <div class="links">
    <a class="plink" href="https://github.com/santinovargasdb/AlToque" target="_blank" rel="noopener"><span>&#128025;</span>GitHub</a>
    <a class="plink" href="https://al-toque-eta.vercel.app" target="_blank" rel="noopener" data-es="Ver en vivo ↗" data-en="View live ↗" data-jp="デモを見る ↗">Ver en vivo &nearr;</a>
  </div>
</div>

<!-- TIER 2: destacado dark -->
<div class="proj-dark reveal">
  <span class="badge" data-es="Liderazgo · IoT · 2024" data-en="Leadership · IoT · 2024" data-jp="リーダーシップ · IoT · 2024">Liderazgo &middot; IoT &middot; 2024</span>
  <h3 data-es="SMART HOME — DOMÓTICA" data-en="SMART HOME — AUTOMATION" data-jp="スマートホーム — 自動化">SMART HOME &mdash; DOMOTICA</h3>
  <p data-es="Maqueta a escala de una smart home funcional con ESP32 conectado por WiFi a una interfaz web. Control de servomotores, LEDs y sensores en tiempo real. Lideré un equipo de 5 personas como PM y dev frontend." data-en="Scale model of a functional smart home with ESP32 connected via WiFi to a web interface. Real-time control of servomotors, LEDs and sensors. Led a team of 5 as PM and frontend developer." data-jp="WiFi接続ESP32とWebインターフェースによるスマートホームの縮尺模型。サーボ・LED・センサーをリアルタイム制御。PMとフロント開発者として5人チームをリード。">Maqueta a escala de una smart home funcional con ESP32 conectado por WiFi a una interfaz web. Control de servomotores, LEDs y sensores en tiempo real. Lidere un equipo de 5 personas como PM y dev frontend.</p>
  <div class="tags">
    <span class="tag">ESP32</span><span class="tag">HTML / CSS</span><span class="tag">WiFi</span><span class="tag">IoT</span><span class="tag">Team Lead x5</span><span class="tag">XAMPP</span>
  </div>
</div>
```

- [ ] **Step 3: HTML — filtro + grid de 7 cards**

```html
<div class="section-label reveal" data-es="Más proyectos" data-en="More projects" data-jp="その他のプロジェクト">Mas proyectos <span class="proj-count" data-count="7">0</span></div>
<div class="filter-bar reveal">
  <button class="filter-pill active" data-filter="all" data-es="Todos" data-en="All" data-jp="すべて">Todos</button>
  <button class="filter-pill" data-filter="fullstack">Full-stack</button>
  <button class="filter-pill" data-filter="data" data-es="Data & IA" data-en="Data & AI" data-jp="データ & AI">Data & IA</button>
</div>
<div class="proj-grid">

  <div class="proj-card reveal" data-cat="fullstack">
    <span class="cat-tag">Full-stack · PWA</span>
    <h4>LOGISWIFT</h4>
    <p class="desc" data-es="PWA mobile-first de logística urbana para un repartidor/vendedor: hoja de ruta del día, registro de ventas al instante, stock del vehículo y cierre de jornada. Pensada para usarse con una mano." data-en="Mobile-first urban-logistics PWA for a delivery driver/vendor: daily route sheet, on-the-spot sales logging, vehicle stock and end-of-day close. Built to be used one-handed." data-jp="配達員・販売員向けモバイルファースト物流PWA：日次ルート、その場での売上記録、車両在庫、日次締め。片手操作を想定。">PWA mobile-first de logistica urbana para un repartidor/vendedor: hoja de ruta del dia, registro de ventas al instante, stock del vehiculo y cierre de jornada. Pensada para usarse con una mano.</p>
    <div class="tags"><span class="tag">React 19</span><span class="tag">TypeScript</span><span class="tag">Vite</span><span class="tag">Tailwind v4</span><span class="tag">Supabase</span><span class="tag">PWA</span></div>
    <div class="links"><a class="plink" href="https://github.com/santinovargasdb/LogiSwift" target="_blank" rel="noopener"><span>&#128025;</span>GitHub</a></div>
  </div>

  <div class="proj-card reveal" data-cat="fullstack">
    <span class="cat-tag">Full-stack · Pagos</span>
    <h4 data-es="OCTAVA CAFÉ" data-en="OCTAVA CAFÉ" data-jp="オクタバ カフェ">OCTAVA CAFE</h4>
    <p class="desc" data-es="Cafetería de especialidad Take Away: catálogo online, personalización del pedido (leche, azúcar, Sin TACC), horario de retiro y pago con Mercado Pago (Checkout Pro + Webhooks IPN)." data-en="Specialty coffee shop, take-away only: online catalog, order customization (milk, sugar, gluten-free), pickup time and Mercado Pago payment (Checkout Pro + IPN webhooks)." data-jp="スペシャルティコーヒーのテイクアウト専門店：オンラインカタログ、注文カスタマイズ（ミルク・砂糖・グルテンフリー）、受取時間、Mercado Pago決済（Checkout Pro + IPN）。">Cafeteria de especialidad Take Away: catalogo online, personalizacion del pedido (leche, azucar, Sin TACC), horario de retiro y pago con Mercado Pago (Checkout Pro + Webhooks IPN).</p>
    <div class="tags"><span class="tag">PHP 8</span><span class="tag">MySQLi</span><span class="tag">JS Vanilla</span><span class="tag">Mercado Pago</span></div>
    <div class="links">
      <a class="plink" href="https://github.com/santinovargasdb/Virtual-Kiosk" target="_blank" rel="noopener"><span>&#128025;</span>GitHub</a>
      <a class="plink" href="https://virtual-kiosk.vercel.app" target="_blank" rel="noopener"><span class="live-dot"></span><span data-es="En vivo" data-en="Live" data-jp="公開中">En vivo</span></a>
    </div>
  </div>

  <div class="proj-card reveal" data-cat="fullstack">
    <span class="cat-tag">Full-stack · PWA</span>
    <h4 data-es="DOJO LEDGER" data-en="DOJO LEDGER" data-jp="道場レジャー">DOJO LEDGER</h4>
    <p class="desc" data-es="PWA mobile-first para trackear hábitos con una economía de monedas gamificada: control de 3 estados a un toque, actualizaciones optimistas y balance en vivo." data-en="Mobile-first PWA to track habits with a gamified coin economy: one-tap 3-state control, optimistic updates and a live balance." data-jp="ゲーム化されたコイン経済で習慣を記録するモバイルファーストPWA：ワンタップの3状態管理、楽観的更新、リアルタイム残高。">PWA mobile-first para trackear habitos con una economia de monedas gamificada: control de 3 estados a un toque, actualizaciones optimistas y balance en vivo.</p>
    <div class="tags"><span class="tag">Next.js 16</span><span class="tag">React 19</span><span class="tag">Tailwind v4</span><span class="tag">Supabase</span></div>
    <div class="links">
      <a class="plink" href="https://github.com/santinovargasdb/habits-tracker-app" target="_blank" rel="noopener"><span>&#128025;</span>GitHub</a>
      <a class="plink" href="https://habits-tracker-app-ten.vercel.app" target="_blank" rel="noopener"><span class="live-dot"></span><span data-es="En vivo" data-en="Live" data-jp="公開中">En vivo</span></a>
    </div>
  </div>

  <div class="proj-card reveal" data-cat="fullstack">
    <span class="cat-tag">E-commerce</span>
    <h4>ARCEA</h4>
    <p class="desc" data-es="Tienda online completa: catálogo de productos, carrito y checkout con Mercado Pago, autenticación de usuarios y SEO técnico (sitemap, OpenGraph)." data-en="Full e-commerce store: product catalog, cart and Mercado Pago checkout, user authentication and technical SEO (sitemap, OpenGraph)." data-jp="本格的なEコマース：商品カタログ、カート、Mercado Pago決済、ユーザー認証、技術的SEO（sitemap・OpenGraph）。">Tienda online completa: catalogo de productos, carrito y checkout con Mercado Pago, autenticacion de usuarios y SEO tecnico (sitemap, OpenGraph).</p>
    <div class="tags"><span class="tag">Next.js 15</span><span class="tag">TypeScript</span><span class="tag">Supabase</span><span class="tag">Mercado Pago</span><span class="tag">shadcn/ui</span></div>
    <div class="links"><a class="plink" href="https://github.com/santinovargasdb/arcea-app" target="_blank" rel="noopener"><span>&#128025;</span>GitHub</a></div>
  </div>

  <div class="proj-card reveal" data-cat="fullstack">
    <span class="cat-tag">App Web · IA</span>
    <h4 data-es="GUARDARROPAS VIRTUAL" data-en="VIRTUAL WARDROBE" data-jp="バーチャルワードローブ">GUARDARROPAS VIRTUAL</h4>
    <p class="desc" data-es="App para digitalizar tu guardarropa: subís tus prendas, las organizás en un clóset virtual y generás combinaciones de outfits con un estilista por IA." data-en="App to digitize your wardrobe: upload your garments, organize them in a virtual closet and generate outfit combinations with an AI stylist." data-jp="ワードローブをデジタル化するアプリ：服をアップロードして仮想クローゼットに整理し、AIスタイリストでコーデを生成。">App para digitalizar tu guardarropa: subis tus prendas, las organizas en un closet virtual y generas combinaciones de outfits con un estilista por IA.</p>
    <div class="tags"><span class="tag">React</span><span class="tag">TypeScript</span><span class="tag">Vite</span><span class="tag">Supabase</span><span class="tag">IA</span></div>
    <div class="links">
      <a class="plink" href="https://github.com/santinovargasdb/Guardarropas-Virtual" target="_blank" rel="noopener"><span>&#128025;</span>GitHub</a>
      <a class="plink" href="https://guardarropas-virtual.vercel.app" target="_blank" rel="noopener"><span class="live-dot"></span><span data-es="En vivo" data-en="Live" data-jp="公開中">En vivo</span></a>
    </div>
  </div>

  <div class="proj-card reveal" data-cat="data">
    <span class="cat-tag">Data & IA · SMATA</span>
    <h4 data-es="MONITOR DE MEDIOS" data-en="MEDIA MONITOR" data-jp="メディアモニター">MONITOR DE MEDIOS</h4>
    <p class="desc" data-es="Solución real para una empresa real: monitor de medios y redes para el Depto. de Prensa de SMATA, con filtrado por reglas, scoring por IA (Gemini) e informes en Word." data-en="Real solution for a real company: media & social monitor for SMATA's Press Dept., with rule-based filtering, AI scoring (Gemini) and Word reports." data-jp="実企業向けの実ソリューション：SMATA広報部のメディア・SNSモニター。ルールベース filtering、AI（Gemini）スコアリング、Wordレポート。">Solucion real para una empresa real: monitor de medios y redes para el Depto. de Prensa de SMATA, con filtrado por reglas, scoring por IA (Gemini) e informes en Word.</p>
    <div class="tags"><span class="tag">Python</span><span class="tag">IA (Gemini)</span><span class="tag">Filtrado img/texto</span><span class="tag">Word</span></div>
    <div class="links">
      <a class="plink" href="https://github.com/santinovargasdb/social-media-filter-engine" target="_blank" rel="noopener"><span>&#128025;</span>GitHub</a>
      <a class="plink" href="https://filtro-redes-sociales-smt.vercel.app" target="_blank" rel="noopener"><span class="live-dot"></span><span data-es="En vivo" data-en="Live" data-jp="公開中">En vivo</span></a>
    </div>
  </div>

  <div class="proj-card reveal" data-cat="data">
    <span class="cat-tag">Python · NLP</span>
    <h4 data-es="INTELIGENCIA DE NOTICIAS" data-en="NEWS INTELLIGENCE" data-jp="ニュースインテリジェンス">INTELIGENCIA DE NOTICIAS</h4>
    <p class="desc" data-es="Sistema modular en Python que extrae noticias por RSS, las categoriza y resume con NLP, traduce fuentes internacionales y genera informes ejecutivos. Interfaz en Streamlit." data-en="Modular Python system that pulls news via RSS, categorizes and summarizes with NLP, translates international sources and generates executive reports. Streamlit interface." data-jp="RSSでニュースを取得し、NLPで分類・要約、海外ソースを翻訳して要約レポートを生成するPythonモジュラーシステム。Streamlit UI。">Sistema modular en Python que extrae noticias por RSS, las categoriza y resume con NLP, traduce fuentes internacionales y genera informes ejecutivos. Interfaz en Streamlit.</p>
    <div class="tags"><span class="tag">Python</span><span class="tag">RSS</span><span class="tag">NLP</span><span class="tag">TextBlob</span><span class="tag">Streamlit</span></div>
    <div class="links"><a class="plink" href="https://github.com/santinovargasdb/monitor-inteligencia-smata" target="_blank" rel="noopener"><span>&#128025;</span>GitHub</a></div>
  </div>

</div>
```

- [ ] **Step 4: Verificar (sin JS de filtro aún)**

Recargar. Expected: AlToque como card grande con degradado + puntos, Smart Home como card oscura, barra de 3 pills de filtro, grid de 7 cards limestone con tag amarillo, título Bebas, tags y links; los "En vivo" tienen dot pulsante. El contador muestra "0" (se anima en Task 10). El filtro todavía no filtra (JS en Task 10). Cambio de idioma OK en todo.

- [ ] **Step 5: Commit**

```bash
git add index.html && git commit -m "feat(redesign): seccion proyectos estrella con tiers y grid de 9 reales"
```

---

### Task 8: Contacto (sección oscura + input píldora mailto) + Footer

Reestilar contacto como sección oscura con título gigante, "input" píldora que dispara mailto, y contact links como cards planas. Footer plano con divisor punteado.

**Files:**
- Modify: `index.html` (CSS contact ~204-221 y footer; HTML `#contacto` ~603-616 y `<footer>` ~618-625)

**Interfaces:**
- Consumes: tokens; `.btn-fire`.
- Produces: `.contact` (sección oscura), `.contact-input` (pill), `.contact-link`.

- [ ] **Step 1: CSS de contacto + footer**

```css
#contacto{background:var(--ink);color:var(--surface)}
.contact-layout{display:grid;grid-template-columns:1fr 1fr;gap:56px;align-items:center}
.contact-big{font-family:var(--font-display);font-size:clamp(44px,6vw,84px);line-height:0.96;letter-spacing:0.02em;margin-bottom:22px}
.contact-big .fire{color:var(--fire2)}
.contact-big .cyan{color:var(--cyan2)}
.contact-sub{font-size:16px;color:rgba(245,240,232,0.7);line-height:1.7;margin-bottom:28px}
.contact-form{display:flex;gap:10px;flex-wrap:wrap}
.contact-input{flex:1;min-width:220px;background:transparent;border:1.5px solid rgba(245,240,232,0.4);border-radius:var(--r-input);padding:14px 24px;color:var(--surface);font-family:var(--font-body);font-weight:500;font-size:15px;outline:none}
.contact-input::placeholder{color:rgba(245,240,232,0.45)}
.contact-input:focus{border-color:var(--fire2)}
.contact-links{display:flex;flex-direction:column;gap:10px}
.contact-link{display:flex;align-items:center;gap:14px;padding:16px 22px;background:rgba(245,240,232,0.06);border:1.5px solid rgba(245,240,232,0.12);border-radius:var(--r-sm);text-decoration:none;color:var(--surface);font-size:14px;font-weight:500;transition:all 0.2s;border-left:4px solid transparent}
.contact-link:nth-child(1){border-left-color:var(--cyan)}
.contact-link:nth-child(2){border-left-color:var(--fire)}
.contact-link:nth-child(3){border-left-color:var(--yellow)}
.contact-link:hover{background:rgba(245,240,232,0.12);transform:translateX(6px)}
.contact-link-icon{font-size:18px;width:24px;text-align:center}
footer{background:var(--ink);color:rgba(245,240,232,0.5);padding:24px 40px;display:flex;justify-content:space-between;align-items:center;font-size:12px;font-weight:500;letter-spacing:0.04em;border-top:1.5px dotted rgba(245,240,232,0.25)}
.footer-colors{display:flex;gap:6px}
.footer-dot{width:9px;height:9px;border-radius:50%}
.cursor-glow{position:fixed;width:340px;height:340px;border-radius:50%;background:radial-gradient(circle,rgba(232,78,27,0.07) 0%,transparent 70%);pointer-events:none;transform:translate(-50%,-50%);transition:left 0.08s ease,top 0.08s ease;z-index:9999}
```

- [ ] **Step 2: HTML de `#contacto`**

Envolver en `.container`. El "input" es decorativo y el botón arma un mailto (no hay backend):

```html
<section id="contacto"><div class="container">
  <div class="contact-layout">
    <div class="reveal-left">
      <div class="contact-big" data-es="TRABAJAMOS<br><span class='cyan'>JUNTOS</span><span class='fire'>?</span>" data-en="SHALL WE<br><span class='cyan'>WORK</span><span class='fire'> TOGETHER?</span>" data-jp="一緒に<br><span class='cyan'>働き</span><span class='fire'>ませんか?</span>">TRABAJAMOS<br><span class="cyan">JUNTOS</span><span class="fire">?</span></div>
      <p class="contact-sub" data-es="Estoy buscando mi primera oportunidad — dependencia o freelance. Si buscás energía, compromiso y ganas de crecer, hablemos." data-en="I'm looking for my first opportunity — employment or freelance. If you want energy, commitment and drive to grow, let's talk." data-jp="最初のチャンスを探しています — 正社員またはフリーランス。エネルギーとコミットメント、成長意欲のある人材をお探しなら、ぜひ。">Estoy buscando mi primera oportunidad &mdash; dependencia o freelance. Si buscas energia, compromiso y ganas de crecer, hablemos.</p>
      <div class="contact-form">
        <input class="contact-input" type="email" id="contactEmail" placeholder="tu@email.com" data-es-ph="tu@email.com" data-en-ph="your@email.com" data-jp-ph="your@email.com" aria-label="Email">
        <button class="btn-fire mag" id="contactSend" data-es="Escribime →" data-en="Write me →" data-jp="連絡する →">Escribime &rarr;</button>
      </div>
    </div>
    <div class="contact-links reveal-right">
      <a href="https://www.linkedin.com/in/santino-vargas-di-buono-7b77b2378" target="_blank" rel="noopener" class="contact-link"><span class="contact-link-icon">in</span>LinkedIn &mdash; Santino Vargas Di Buono</a>
      <a href="mailto:Santivargasdb@gmail.com" class="contact-link"><span class="contact-link-icon">&#9993;</span>Santivargasdb@gmail.com</a>
      <a href="https://github.com/santinovargasdb" target="_blank" rel="noopener" class="contact-link"><span class="contact-link-icon">&#128025;</span>GitHub &mdash; santinovargasdb</a>
    </div>
  </div>
</div></section>
```

- [ ] **Step 3: Verificar**

Recargar. Expected: sección de contacto oscura, título gigante con acentos de color, input píldora + botón naranja, links como cards con borde de color que se desplazan en hover. Footer oscuro con divisor punteado.

- [ ] **Step 4: Commit**

```bash
git add index.html && git commit -m "feat(redesign): contacto oscuro con input pildora y footer plano"
```

---

### Task 9: Responsive (media queries actualizadas)

Reescribir las media queries para el nuevo layout (nav píldora, hero, grid de proyectos, filtro, contacto).

**Files:**
- Modify: `index.html` (bloque `@media` ~223-310)

**Interfaces:**
- Consumes: todas las clases nuevas.

- [ ] **Step 1: Reemplazar las media queries**

```css
@media (max-width:900px){
  nav{padding:12px 18px;flex-wrap:wrap;gap:10px}
  .nav-logo{font-size:13px}
  .nav-pill{order:3;width:100%;justify-content:center;overflow-x:auto}
  .hero{grid-template-columns:1fr;padding:48px 20px;gap:32px;min-height:auto}
  .hero-right{max-width:360px}
  .contact-layout{grid-template-columns:1fr;gap:32px}
  .skills-strip{grid-template-columns:1fr 1fr}
  .skill-col{border-left:0;padding:8px 0 8px 0;border-top:1.5px dotted var(--dot);padding-top:18px}
  .about-layout{grid-template-columns:1fr;gap:32px}
}
@media (max-width:768px){
  section{padding:52px 20px}
  .proj-grid{grid-template-columns:1fr}
  .proj-hero,.proj-dark{padding:32px 24px}
  footer{flex-direction:column;gap:10px;text-align:center}
  .cursor-glow{display:none}
}
@media (max-width:480px){
  .nav-pill{gap:2px;padding:4px 6px}
  .nav-pill a{padding:7px 10px;font-size:13px}
  .hero-name{font-size:clamp(56px,17vw,90px)}
  .skills-strip{grid-template-columns:1fr}
  .about-stats{grid-template-columns:repeat(3,1fr)}
}
```

- [ ] **Step 2: Verificar en 3 anchos**

Con DevTools (F12 → toggle device toolbar), probar ~1440px, ~768px, ~380px. Expected: sin scroll horizontal; hero apila; nav usable; grid de proyectos a 1 columna en mobile; contacto apila. Cambio de idioma OK.

- [ ] **Step 3: Commit**

```bash
git add index.html && git commit -m "feat(redesign): responsive para el nuevo layout"
```

---

### Task 10: JavaScript interactivo (filtro, contador, botones magnéticos, parallax, i18n del placeholder)

Ampliar el `<script>`: mantener switcher de idioma + scroll-reveal + cursor glow; agregar filtro de proyectos, contador, botones magnéticos, parallax del halftone, mailto del contacto, y placeholder i18n. Todo bajo guard de reduce-motion donde corresponde.

**Files:**
- Modify: `index.html` (bloque `<script>` ~627-673)

**Interfaces:**
- Consumes: `.filter-pill[data-filter]`, `.proj-card[data-cat]`, `.proj-count[data-count]`, `.mag`, `.photo-halftone`, `#contactEmail`, `#contactSend`, `data-*-ph`.

- [ ] **Step 1: Conservar el switcher de idioma existente y extenderlo para placeholders**

En `applyLang(lang)`, después del loop de `data-*`, agregar el manejo de placeholders:

```javascript
document.querySelectorAll('[data-es-ph],[data-en-ph],[data-jp-ph]').forEach(el=>{
  const ph = el.getAttribute('data-'+key+'-ph');
  if(ph!==null) el.setAttribute('placeholder',ph);
});
```
(Mantener intactos el resto del switcher, el `htmlLang` map y el highlight de `.lang-btn`.)

- [ ] **Step 2: Filtro de proyectos**

```javascript
const filterPills = document.querySelectorAll('.filter-pill');
const projCards = document.querySelectorAll('.proj-card');
filterPills.forEach(pill=>{
  pill.addEventListener('click',()=>{
    filterPills.forEach(p=>p.classList.remove('active'));
    pill.classList.add('active');
    const f = pill.getAttribute('data-filter');
    projCards.forEach(card=>{
      const show = f==='all' || card.getAttribute('data-cat')===f;
      card.classList.toggle('hide',!show);
    });
  });
});
```

- [ ] **Step 3: Contador de proyectos (al entrar en viewport)**

```javascript
const reduce = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
document.querySelectorAll('.proj-count').forEach(el=>{
  const target = parseInt(el.getAttribute('data-count'),10)||0;
  if(reduce){ el.textContent = target; return; }
  const io = new IntersectionObserver((ents)=>{
    ents.forEach(e=>{
      if(!e.isIntersecting) return;
      io.unobserve(el);
      let n=0; const step=Math.max(1,Math.round(target/24));
      const t=setInterval(()=>{ n+=step; if(n>=target){n=target;clearInterval(t);} el.textContent=n; },28);
    });
  },{threshold:1});
  io.observe(el);
});
```

- [ ] **Step 4: Botones magnéticos**

```javascript
if(!reduce){
  document.querySelectorAll('.mag').forEach(el=>{
    el.addEventListener('mousemove',e=>{
      const r=el.getBoundingClientRect();
      const mx=e.clientX-(r.left+r.width/2), my=e.clientY-(r.top+r.height/2);
      el.style.transform=`translate(${mx*0.15}px,${my*0.25}px)`;
    });
    el.addEventListener('mouseleave',()=>{ el.style.transform=''; });
  });
}
```

- [ ] **Step 5: Parallax sutil del bloque halftone**

```javascript
if(!reduce){
  const ph=document.querySelector('.photo-halftone');
  if(ph){ window.addEventListener('scroll',()=>{ const y=window.scrollY; if(y<900) ph.style.transform=`translateY(${y*0.04}px)`; },{passive:true}); }
}
```

- [ ] **Step 6: Mailto del contacto**

```javascript
const send=document.getElementById('contactSend'), email=document.getElementById('contactEmail');
if(send){ send.addEventListener('click',()=>{
  const from=(email && email.value.trim())?email.value.trim():'';
  const subj=encodeURIComponent('Contacto desde el portfolio');
  const body=encodeURIComponent(from?('Mi email: '+from+'\n\n'):'');
  window.location.href=`mailto:Santivargasdb@gmail.com?subject=${subj}&body=${body}`;
}); }
```

- [ ] **Step 7: Verificar todo**

Recargar. Expected:
- Filtro: click en "Full-stack" muestra 5 cards, "Data & IA" muestra 2, "Todos" muestra 7.
- Contador: al llegar a la sección, el número sube de 0 a 7.
- Botones magnéticos: `.hero-tag`, CTAs y botón de contacto siguen levemente al cursor.
- Parallax: el bloque de la foto se mueve suave al scrollear.
- Contacto: escribir un email y "Escribime →" abre el cliente de correo con ese dato.
- Con `prefers-reduced-motion: reduce` (DevTools → Rendering → Emulate CSS prefers-reduced-motion): sin magnetismo/parallax, contador muestra 7 directo, todo usable.
- Consola sin errores. Switcher ES/EN/JP funciona en todo el contenido nuevo (incluye placeholder del input).

- [ ] **Step 8: Commit**

```bash
git add index.html && git commit -m "feat(redesign): filtro, contador, botones magneticos y parallax"
```

---

### Task 11: Verificación final, limpieza y merge

Chequeos globales de calidad y decisión de integración.

**Files:**
- Modify: `index.html` (limpieza de CSS muerto si quedó)
- Modify: `README.md` (actualizar "Características" con proyectos + estilo Caldera)

- [ ] **Step 1: Chequeo de sombras (debe ser 0)**

```bash
grep -c "box-shadow" index.html
```
Expected: `0`. Si aparece alguna, eliminarla.

- [ ] **Step 2: Chequeo de CSS/HTML muerto**

Buscar clases viejas sin uso y removerlas del CSS: `.hero-slash`, `.hero-slash-2`, `.blob`, `.blobmove`, `.projects-layout`, `.project-main*`, `.project-mini*`, `.project-side`, `.project-card*`, `.projects-grid`, `.skills-layout`, `.identity-card` (verificar cuáles siguen en uso). Confirmar con:
```bash
grep -n "project-main\|hero-slash\|skills-layout\|projects-grid" index.html
```
Expected: sin coincidencias en HTML (solo podrían quedar en CSS a limpiar).

- [ ] **Step 3: Verificación funcional completa en navegador**

Recorrer todo el sitio a 1440/768/380px. Checklist:
- Sin scroll horizontal en ningún ancho.
- Todos los links externos abren el destino correcto (AlToque live, demos, GitHub, LinkedIn, mail).
- Los 3 idiomas cambian TODO el texto visible.
- Foto del hero visible y con tratamiento halftone.
- Filtro, contador, hovers, magnetismo y parallax funcionan.
- Consola sin errores.

- [ ] **Step 4: Actualizar README**

Actualizar la sección "Características" para reflejar: rediseño estilo editorial "Caldera", sección de proyectos con 9 proyectos reales, filtro por categoría, animaciones (halftone, contador, botones magnéticos, parallax), y accesibilidad (reduce-motion).

- [ ] **Step 5: Commit final**

```bash
git add index.html README.md && git commit -m "chore(redesign): limpieza, README y verificacion final"
```

- [ ] **Step 6: Decisión de integración**

Invocar la skill `superpowers:finishing-a-development-branch` para elegir merge a `main` / PR / seguir. (No mergear sin confirmación del usuario.)

---

## Self-Review

**Cobertura del spec:**
- Tokens/colores/tipografía/formas/flat → Task 1. ✓
- Nav píldora + redes → Task 2. ✓
- Hero halftone con foto → Task 3. ✓
- Ticker + títulos → Task 4. ✓
- Sobre mí (cards planas, pills, stats chicas, sin franja) → Task 5. ✓
- Skills tira punteada → Task 6. ✓
- Proyectos estrella (tier1 AlToque + tier2 Smart Home + grid filtrable 7, contador, 9 proyectos reales) → Task 7 + JS Task 10. ✓
- Contacto oscuro + input píldora mailto + footer → Task 8. ✓
- Responsive → Task 9. ✓
- Animaciones (reveal, halftone vivo, magnético, contador, hover-lift, parallax, ticker, cursor glow, reduce-motion) → Tasks 1/3/7/10. ✓
- Multi-idioma preservado + placeholder i18n → Tasks (todas) + Task 10. ✓
- Foto base64 preservada → Task 3 (nota explícita). ✓
- Sin sombras → Task 1 + verificación Task 11. ✓

**Placeholders:** sin TBD/TODO; todo el CSS/HTML/JS está escrito; copy trilingüe incluido para los 9 proyectos.

**Consistencia de tipos/nombres:** clases usadas de forma consistente: `.mag` (definida Task 3, usada Task 10), `.proj-count[data-count]` (Task 7 → Task 10), `.filter-pill[data-filter]` + `.proj-card[data-cat]` con valores `fullstack`/`data`/`all` (Task 7 → Task 10), `.btn-fire` (Task 3, reusada Task 8), `data-*-ph` (Task 8 → Task 10). ✓
