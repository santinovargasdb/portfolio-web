# Barril de Proyectos (carrusel vertical 3D) — Plan de Implementación

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans (inline recommended — this plan needs controller-side browser screenshot capture + visual iteration) or subagent-driven-development. Steps use checkbox (`- [ ]`) syntax.

**Goal:** Reemplazar la sección `#proyectos` por un carrusel vertical 3D tipo "barril de revólver" con 9 cards uniformes horizontales (mitad texto + mitad visual con botón grande), controlado por scroll + arrastrar.

**Architecture:** Un único `index.html` estático. Se elimina el HTML/CSS/JS de la sección de proyectos actual (tiers + filtro + grid + contador) y se reemplaza por: (a) CSS de un stage 3D (`perspective`) con un `.barrel` (`preserve-3d`, `rotateX`) y 9 `.barrel-card` como facetas de un cilindro; (b) 9 cards uniformes con copy trilingüe reusado + screenshots (5 desplegados) o covers diseñados (4); (c) JS vanilla que posiciona las facetas, detecta la card activa, y maneja drag/wheel/flechas/teclado con snap por `requestAnimationFrame`; (d) fallback a stack vertical en mobile y `prefers-reduced-motion`.

**Tech Stack:** HTML5, CSS3 (transform 3D, perspective, clamp), JavaScript vanilla (Pointer Events, requestAnimationFrame, matchMedia). Screenshots capturados con Playwright (MCP) y embebidos como JPEG base64.

## Global Constraints

- **Un solo archivo `index.html`.** Sin framework/bundler/build, sin librerías JS externas. Deploy Netlify.
- **Multi-idioma ES/EN/JP** con `data-es`/`data-en`/`data-jp` en todo texto. Reusar los copys actuales de cada proyecto.
- **Foto base64 del hero intacta** (1 ocurrencia, 186252 bytes). Nunca tocarla.
- **Sin `box-shadow`** en el CSS final. Radios: cards 40px, píldoras 800px. Bebas display + DM Sans 500 body.
- **Respetar `prefers-reduced-motion`** y degradar en mobile (≤900px / touch) a stack vertical.
- **Links exactos:** GitHub `github.com/santinovargasdb/<repo>`; demos Vercel exactas del catálogo del spec.
- **Rama:** `redesign/projects-barrel` (ya creada).
- **Catálogo fuente de verdad:** `docs/superpowers/specs/2026-09-29-projects-barrel-carousel-design.md` (tabla de 9 proyectos).

**Verificación local (todas las tareas):**
```bash
cd "C:/Users/accsoc/portfolio/portfolio-web"
python -m http.server 8123   # abrir http://localhost:8123/?v=<algo> con cache-bust
```
Integridad: `grep -c 'data:image/png\|data:image/gif' index.html` no aplica; para la foto usar `grep -c 'data:image' index.html` (subirá al embeber screenshots) — verificar la foto por separado con la firma de 186252 bytes del hero. `grep -c box-shadow index.html` = 0.

---

### Task 1: Capturar y comprimir los 5 screenshots (controller-side)

Capturar los 5 sitios desplegados y dejar cada uno como un archivo `data:image/jpeg;base64,...` en el scratchpad, para inyectarlos luego.

**Files:**
- Create (scratchpad): `shot-altoque.txt`, `shot-octava.txt`, `shot-dojo.txt`, `shot-guardarropas.txt`, `shot-monitor.txt` (cada uno un data-URI JPEG)

**Interfaces:**
- Produces: 5 archivos data-URI, uno por proyecto desplegado, mapeados a `data-shot`:
  `altoque` → al-toque-eta.vercel.app · `octava` → virtual-kiosk.vercel.app · `dojo` → habits-tracker-app-ten.vercel.app · `guardarropas` → guardarropas-virtual.vercel.app · `monitor` → filtro-redes-sociales-smt.vercel.app

- [ ] **Step 1: Capturar cada sitio (viewport 1000×680, JPEG)**

Para cada URL: `browser_navigate` a la URL, `browser_resize` 1000×680, esperar carga (~1.5s), `browser_take_screenshot` con `type:jpeg` a un archivo (p.ej. `shot-altoque.jpg`) dentro de la raíz permitida del workspace. Si un sitio no carga o muestra login, capturar la landing igual (o usar cover si queda inservible — anotarlo).

- [ ] **Step 2: Convertir cada JPEG a data-URI base64**

Con Python (ya disponible en `.../Python312/python`):
```python
import base64, pathlib
for name in ["altoque","octava","dojo","guardarropas","monitor"]:
    p = pathlib.Path(f"{name}.jpg")   # ajustar ruta real del screenshot
    b64 = base64.b64encode(p.read_bytes()).decode()
    out = f"data:image/jpeg;base64,{b64}"
    pathlib.Path(f"<SCRATCHPAD>/shot-{name}.txt").write_text(out, encoding="utf-8")
    print(name, len(out)//1024, "KB")
```
Objetivo: cada data-URI ≲ 160KB. Si alguno supera mucho, re-capturar a menor tamaño (900×612) o aceptar (total objetivo ≲ 700KB).

- [ ] **Step 3: Verificar**

Los 5 `.txt` existen, empiezan con `data:image/jpeg;base64,`, y la suma de tamaños es razonable (≲ 700KB). No se toca `index.html` en esta tarea.

- [ ] **Step 4 (no commit):** esta tarea no modifica `index.html`; los `.txt` viven en scratchpad (no se commitean).

---

### Task 2: CSS del barril + cards uniformes (estilos, sin construir cards)

Reemplazar el bloque CSS de proyectos actual por el CSS del barril, las cards uniformes, covers, badges, CTA grande, flechas, y el fallback flat. Tras esta tarea la sección puede quedar vacía/rota hasta Task 3 — es esperado.

**Files:**
- Modify: `index.html` (bloque CSS de proyectos: reglas `.proj-hero`, `.proj-dark`, `.tags/.tag`, `.plink`, `.filter-bar/.filter-pill`, `.proj-count`, `.proj-grid`, `.proj-card`, `.cat-tag`, `.live-dot` → reemplazar por las nuevas)

**Interfaces:**
- Consumes: tokens (`--surface`, `--ink`, `--muted`, `--fire`, `--cyan`, `--yellow`, `--border`, `--r-card`, `--r-pill`, `--font-display`).
- Produces: clases `.barrel-stage`, `.barrel`, `.barrel-card`, `.bcard-text`, `.bcard-visual`, `.bcard-cover`, `.bcard-badge`, `.bcard-cta`, `.bcard-tags/.btag`, `.barrel-nav/.barrel-up/.barrel-down`, y el modificador `.barrel-stage.flat`. Variables `--cw`, `--ch`.

- [ ] **Step 1: Insertar el CSS del barril (reemplazando el CSS viejo de proyectos)**

```css
/* ===== PROYECTOS — barril 3D ===== */
.barrel-stage{--cw:min(1000px,94%);--ch:clamp(300px,44vh,344px);position:relative;height:clamp(430px,62vh,560px);perspective:1500px;overflow:hidden;touch-action:pan-y;outline:none}
.barrel{position:absolute;inset:0;transform-style:preserve-3d;will-change:transform}
.barrel-card{position:absolute;top:50%;left:50%;width:var(--cw);height:var(--ch);margin-left:calc(var(--cw)/-2);margin-top:calc(var(--ch)/-2);display:grid;grid-template-columns:1fr 1fr;background:var(--surface);border:1.5px solid var(--border);border-radius:var(--r-card);overflow:hidden;backface-visibility:hidden;transition:opacity .12s linear}
.barrel-card.active{border-color:var(--fire)}
.bcard-text{padding:clamp(20px,3vw,36px);display:flex;flex-direction:column;justify-content:center;gap:12px;min-width:0}
.bcard-cat{align-self:flex-start;font-size:11px;font-weight:700;letter-spacing:.06em;text-transform:uppercase;padding:5px 12px;border-radius:var(--r-pill);background:var(--yellow);color:var(--ink)}
.bcard-title{font-family:var(--font-display);font-size:clamp(30px,3.4vw,44px);line-height:.96;letter-spacing:.5px;color:var(--ink)}
.bcard-desc{font-size:14px;line-height:1.6;color:var(--muted);display:-webkit-box;-webkit-line-clamp:4;-webkit-box-orient:vertical;overflow:hidden}
.bcard-tags{display:flex;flex-wrap:wrap;gap:6px}
.btag{font-size:11px;font-weight:600;padding:4px 10px;border-radius:var(--r-pill);background:var(--surface-2);color:var(--muted)}
.bcard-actions{display:flex;align-items:center;gap:14px;margin-top:6px}
.bcard-cta{display:inline-flex;align-items:center;gap:8px;background:var(--fire);color:#fff;font-weight:600;font-size:15px;padding:12px 22px;border-radius:var(--r-pill);text-decoration:none;transition:background .2s,transform .2s}
.bcard-cta:hover{background:var(--ink)}
.bcard-ghost{display:inline-flex;align-items:center;gap:6px;font-size:13px;font-weight:700;text-transform:uppercase;letter-spacing:.04em;color:var(--ink);text-decoration:none;border-bottom:2px solid transparent;padding-bottom:2px}
.bcard-ghost:hover{border-bottom-color:var(--fire)}
.bcard-note{font-size:12px;font-weight:600;color:var(--muted)}
/* visual half */
.bcard-visual{position:relative;overflow:hidden;background:var(--ink);display:block;text-decoration:none}
.bcard-visual img{width:100%;height:100%;object-fit:cover;object-position:left top;display:block}
.bcard-visual::after{content:"";position:absolute;inset:0;background:linear-gradient(90deg,rgba(245,240,232,.14),transparent 22%);pointer-events:none}
.bcard-live{position:absolute;top:14px;left:14px;z-index:2;display:inline-flex;align-items:center;gap:6px;font-size:11px;font-weight:700;text-transform:uppercase;letter-spacing:.05em;color:#fff;background:rgba(7,6,7,.55);backdrop-filter:blur(4px);padding:6px 12px;border-radius:var(--r-pill)}
.bcard-live::before{content:"";width:7px;height:7px;border-radius:50%;background:#3ddc84}
/* designed cover (no deploy) */
.bcard-cover{position:absolute;inset:0;background:linear-gradient(135deg,var(--cyan) 0%,var(--fire) 100%);display:flex;flex-direction:column;justify-content:center;gap:14px;padding:28px;color:#fff;text-align:left}
.bcard-cover::after{content:"";position:absolute;inset:0;background-image:radial-gradient(rgba(255,255,255,.5) 18%,transparent 19%);background-size:15px 15px;-webkit-mask-image:linear-gradient(200deg,#000,transparent 60%);mask-image:linear-gradient(200deg,#000,transparent 60%);opacity:.35;pointer-events:none}
.bcard-cover .cv-name{position:relative;font-family:var(--font-display);font-size:clamp(34px,4vw,54px);line-height:.94;letter-spacing:1px}
.bcard-cover .cv-badges{position:relative;display:flex;flex-wrap:wrap;gap:6px}
.bcard-cover .cv-badges span{font-size:11px;font-weight:600;padding:4px 10px;border-radius:var(--r-pill);background:rgba(255,255,255,.2)}
/* nav arrows + hint */
.barrel-nav{position:absolute;right:14px;top:50%;transform:translateY(-50%);z-index:5;display:flex;flex-direction:column;gap:10px}
.barrel-nav button{width:44px;height:44px;border-radius:var(--r-pill);border:1.5px solid var(--border);background:var(--surface);color:var(--ink);font-size:16px;cursor:pointer;display:grid;place-items:center;transition:all .18s}
.barrel-nav button:hover{background:var(--ink);color:var(--surface);border-color:var(--ink)}
.barrel-hint{text-align:center;font-size:12px;font-weight:600;letter-spacing:.04em;color:var(--muted);margin-top:14px}
.barrel-stage.grabbing{cursor:grabbing}
.barrel-stage{cursor:grab}
/* flat fallback (mobile / reduce-motion) */
.barrel-stage.flat{height:auto;perspective:none;overflow:visible;cursor:default}
.barrel-stage.flat .barrel{position:static;transform:none!important;display:flex;flex-direction:column;gap:16px}
.barrel-stage.flat .barrel-card{position:static;transform:none!important;opacity:1!important;pointer-events:auto!important;margin:0;width:100%;height:auto;min-height:280px}
.barrel-stage.flat .barrel-nav,.barrel-stage.flat .barrel-hint{display:none}
@media (max-width:900px){
  .barrel-stage{height:auto;perspective:none;overflow:visible;cursor:default}
  .barrel{position:static;transform:none!important;display:flex;flex-direction:column;gap:16px}
  .barrel-card{position:static;transform:none!important;opacity:1!important;pointer-events:auto!important;margin:0;width:100%;height:auto;grid-template-columns:1fr}
  .bcard-visual{min-height:200px;order:-1}
  .barrel-nav,.barrel-hint{display:none}
}
```

- [ ] **Step 2: Verificar**

`grep -c box-shadow index.html` = 0. `grep -c 'barrel-stage' index.html` ≥1 (CSS). `grep -c 'data:image' index.html` = 1 y foto de 186252 bytes intacta. La página sirve (HTTP 200). La sección de proyectos puede verse vacía/rota (esperado hasta Task 3).

- [ ] **Step 3: Commit**

```bash
git add index.html && git commit -m "feat(barrel): CSS del carrusel 3D y cards uniformes"
```

---

### Task 3: Construir las 9 cards uniformes (HTML) + reemplazar la sección

Reemplazar el HTML interno de `#proyectos` por el `.barrel-stage` con las 9 `.barrel-card`. Reusar el copy ES/EN/JP y links de cada proyecto (del HTML actual y del catálogo del spec). Los 5 desplegados llevan `<img data-shot="..." loading="lazy" alt="...">` con `src` vacío (se inyecta en Task 4); los 4 restantes llevan `.bcard-cover`.

**Files:**
- Modify: `index.html` (HTML de `#proyectos`: quitar tier1/tier2/filter-bar/grid; poner el `.barrel-stage`)

**Interfaces:**
- Consumes: clases CSS de Task 2.
- Produces: `.barrel-stage` con `tabindex="0"`, un `.barrel` con 9 `.barrel-card`, cada visual desplegada con `img[data-shot]`, `.barrel-nav` con `.barrel-up`/`.barrel-down`, y `.barrel-hint`. Estas son las que consume el JS de Task 5.

- [ ] **Step 1: Reemplazar el interior de `<section id="proyectos"><div class="container">`**

Mantener `.section-label` ("Trabajo") + `.section-title` ("PROYECTOS"). Después, el stage. Template de card **desplegada** (ej. AlToque) y **cover** (ej. LogiSwift):

```html
<div class="barrel-stage" id="barrel" tabindex="0" aria-label="Carrusel de proyectos">
  <div class="barrel">

    <!-- CARD DESPLEGADA (screenshot) -->
    <article class="barrel-card">
      <div class="bcard-text">
        <span class="bcard-cat">Full-stack</span>
        <h3 class="bcard-title">ALTOQUE</h3>
        <p class="bcard-desc" data-es="Marketplace que conecta personas con profesionales de oficios verificados para urgencias y trabajos agendados. Matching por geolocalización, pago con Mercado Pago e instalable como PWA." data-en="Marketplace connecting people with verified trade professionals for emergencies and scheduled jobs. Geolocation matching, Mercado Pago payments and installable as a PWA." data-jp="認証済みの職人とユーザーをつなぐマーケットプレイス。位置情報マッチング、Mercado Pago決済、PWA対応。">Marketplace que conecta personas con profesionales de oficios verificados para urgencias y trabajos agendados. Matching por geolocalizacion, pago con Mercado Pago e instalable como PWA.</p>
        <div class="bcard-tags"><span class="btag">Next.js 15</span><span class="btag">TypeScript</span><span class="btag">Supabase</span><span class="btag">PostGIS</span><span class="btag">Mercado Pago</span></div>
        <div class="bcard-actions">
          <a class="bcard-cta" href="https://al-toque-eta.vercel.app" target="_blank" rel="noopener" data-es="Ver en vivo →" data-en="View live →" data-jp="デモを見る →">Ver en vivo →</a>
          <a class="bcard-ghost" href="https://github.com/santinovargasdb/AlToque" target="_blank" rel="noopener"><span>&#128025;</span>GitHub</a>
        </div>
      </div>
      <a class="bcard-visual" href="https://al-toque-eta.vercel.app" target="_blank" rel="noopener" aria-label="Ver AlToque en vivo">
        <span class="bcard-live" data-es="En vivo" data-en="Live" data-jp="公開中">En vivo</span>
        <img data-shot="altoque" src="" loading="lazy" alt="Captura de AlToque">
      </a>
    </article>

    <!-- CARD COVER (sin deploy) -->
    <article class="barrel-card">
      <div class="bcard-text">
        <span class="bcard-cat">Full-stack</span>
        <h3 class="bcard-title">LOGISWIFT</h3>
        <p class="bcard-desc" data-es="PWA mobile-first de logística urbana para un repartidor: hoja de ruta del día, registro de ventas al instante, stock del vehículo y cierre de jornada. Pensada para usarse con una mano." data-en="Mobile-first urban-logistics PWA for a delivery driver: daily route sheet, on-the-spot sales logging, vehicle stock and end-of-day close. Built to be used one-handed." data-jp="配達員向けモバイルファースト物流PWA：日次ルート、その場での売上記録、車両在庫、日次締め。片手操作を想定。">PWA mobile-first de logistica urbana para un repartidor: hoja de ruta del dia, registro de ventas al instante, stock del vehiculo y cierre de jornada. Pensada para usarse con una mano.</p>
        <div class="bcard-tags"><span class="btag">React 19</span><span class="btag">TypeScript</span><span class="btag">Vite</span><span class="btag">Tailwind v4</span><span class="btag">Supabase</span></div>
        <div class="bcard-actions">
          <a class="bcard-cta" href="https://github.com/santinovargasdb/LogiSwift" target="_blank" rel="noopener" data-es="Ver en GitHub →" data-en="View on GitHub →" data-jp="GitHubで見る →">Ver en GitHub →</a>
        </div>
      </div>
      <a class="bcard-visual" href="https://github.com/santinovargasdb/LogiSwift" target="_blank" rel="noopener" aria-label="Ver LogiSwift en GitHub">
        <div class="bcard-cover"><div class="cv-name">LOGI<br>SWIFT</div><div class="cv-badges"><span>PWA</span><span>React 19</span><span>Supabase</span></div></div>
      </a>
    </article>

    <!-- ... las otras 7 cards, mismo patrón ... -->

  </div>
  <div class="barrel-nav">
    <button class="barrel-up" aria-label="Anterior">▲</button>
    <button class="barrel-down" aria-label="Siguiente">▼</button>
  </div>
</div>
<p class="barrel-hint" data-es="Girá con scroll o arrastrando · o usá las flechas" data-en="Spin with scroll or drag · or use the arrows" data-jp="スクロールかドラッグで回転 · または矢印で">Girá con scroll o arrastrando · o usá las flechas</p>
```

**Las 9 cards** (usar copy/tags/links exactos del catálogo del spec y del HTML actual):
1. AlToque — Full-stack — screenshot `altoque` — CTA live al-toque-eta.vercel.app + GitHub AlToque
2. Octava Café — Full-stack — screenshot `octava` — CTA live virtual-kiosk.vercel.app + GitHub Virtual-Kiosk
3. Dojo Ledger — Full-stack — screenshot `dojo` — CTA live habits-tracker-app-ten.vercel.app + GitHub habits-tracker-app
4. Guardarropas Virtual — Full-stack · IA — screenshot `guardarropas` — CTA live guardarropas-virtual.vercel.app + GitHub Guardarropas-Virtual
5. Monitor de Medios — Data & IA — screenshot `monitor` — CTA live filtro-redes-sociales-smt.vercel.app + GitHub social-media-filter-engine
6. LogiSwift — Full-stack — cover — CTA GitHub LogiSwift
7. Arcea — Full-stack — cover — CTA GitHub arcea-app
8. Inteligencia de Noticias — Data & IA — cover — CTA GitHub monitor-inteligencia-smata
9. Smart Home — Domótica — IoT — cover — sin CTA de link; en `.bcard-actions` va `<span class="bcard-note" data-es="Proyecto escolar · 2024" data-en="School project · 2024" data-jp="学校プロジェクト · 2024">Proyecto escolar · 2024</span>` y el `.bcard-visual` es un `<div>` (no `<a>`) con el cover.

Reusar los `data-es/en/jp` de descripciones y tags de cada proyecto tal como están hoy en el HTML (o del catálogo). Todo `&` en texto va como `&amp;`.

- [ ] **Step 2: Verificar**

`grep -c 'barrel-card' index.html` = 9 (o 9 en HTML). `grep -c 'data-shot=' index.html` = 5. `grep -c 'bcard-cover' index.html` ≥4 (HTML). Old classes fuera: `grep -c 'proj-hero\|proj-dark\|filter-pill\|proj-grid\|proj-card\|proj-count' index.html` = 0 (o solo en CSS ya reemplazado → debería ser 0). `#proyectos` con `.container`. Todas las URLs presentes. `grep -c 'data:image' index.html` = 1 (los shots aún vacíos). HTTP 200. Sin `box-shadow`.

- [ ] **Step 3: Commit**

```bash
git add index.html && git commit -m "feat(barrel): 9 cards uniformes con visual + CTA grande"
```

---

### Task 4: Inyectar los 5 screenshots en las cards

Reemplazar el `src=""` de cada `img[data-shot]` con el data-URI JPEG capturado en Task 1, mediante un script Python (para no pasar 500KB de base64 por edición manual).

**Files:**
- Modify: `index.html` (los 5 `img[data-shot]` src)

- [ ] **Step 1: Script de inyección**

```python
import pathlib, re
SP = r"<SCRATCHPAD>"
html = pathlib.Path("index.html").read_text(encoding="utf-8")
for name in ["altoque","octava","dojo","guardarropas","monitor"]:
    uri = pathlib.Path(f"{SP}/shot-{name}.txt").read_text(encoding="utf-8").strip()
    # reemplaza  data-shot="name" src=""  ->  data-shot="name" src="<uri>"
    pat = re.compile(r'(data-shot="'+name+r'"\s+src=")(")')
    html, n = pat.subn(lambda m: m.group(1)+uri+'"', html, count=1)
    assert n == 1, f"no se inyectó {name} (n={n})"
pathlib.Path("index.html").write_text(html, encoding="utf-8")
print("ok")
```

- [ ] **Step 2: Verificar**

`grep -c 'data:image/jpeg' index.html` = 5. `grep -c 'data-shot=".*" src=""' index.html` = 0 (ninguno vacío). Foto del hero intacta (`grep -c 'data:image/png\|data:image' ...` sube a 6 total; la del hero sigue con 186252 bytes — verificar con `grep -o 'data:image/png;base64,[^"]*' index.html | head -c 40` o la firma conocida). `grep -c box-shadow index.html` = 0. HTTP 200; las 5 imágenes se ven en el navegador.

- [ ] **Step 3: Commit**

```bash
git add index.html && git commit -m "feat(barrel): embeber screenshots de los 5 proyectos desplegados"
```

---

### Task 5: JS del barril (posición 3D, control, snap, activa, fallback)

Extender el `<script>`: quitar el JS del filtro y del contador; agregar el módulo del barril. Mantener switcher de idioma, cursor-glow, scroll-reveal, magnético, parallax, mailto y `applyLang(currentLang)` intactos.

**Files:**
- Modify: `index.html` (bloque `<script>`: quitar `.filter-pill`/`.proj-count`; agregar barril)

**Interfaces:**
- Consumes: `.barrel-stage#barrel`, `.barrel`, `.barrel-card` (×9), `.barrel-up`, `.barrel-down`.

- [ ] **Step 1: Quitar el JS del filtro y del contador**

Eliminar los bloques que referencian `.filter-pill`, `filterPills`, `projCards` (filtro) y `.proj-count` (count-up). Dejar el resto del script intacto.

- [ ] **Step 2: Agregar el módulo del barril (antes de cerrar `</script>`)**

```javascript
// ── Barril de proyectos ──────────────────────────────────────────
(function(){
  const stage = document.getElementById('barrel');
  if(!stage) return;
  const barrel = stage.querySelector('.barrel');
  const cards = [...barrel.querySelectorAll('.barrel-card')];
  const N = cards.length; if(!N) return;
  const STEP = 360 / N;
  const mq = window.matchMedia('(max-width:900px)');
  const reduce = window.matchMedia('(prefers-reduced-motion: reduce)').matches;

  let R = 460, angle = 0, target = 0, raf = null;

  function computeR(){
    const h = cards[0].offsetHeight || 320;
    R = Math.round((h/2) / Math.tan((STEP/2)*Math.PI/180)) + 24;
  }
  function layout(){
    computeR();
    cards.forEach((c,i)=>{ c.style.transform = `rotateX(${i*STEP}deg) translateZ(${R}px)`; });
  }
  function render(){
    barrel.style.transform = `translateZ(${-R}px) rotateX(${angle}deg)`;
    const act = (((Math.round(-angle/STEP)) % N) + N) % N;
    cards.forEach((c,i)=>{
      let d = (((angle + i*STEP) % 360) + 360) % 360; if(d>180) d = 360 - d;
      const isActive = i === act;
      c.classList.toggle('active', isActive);
      c.style.opacity = isActive ? '1' : String(Math.max(0.06, 1 - d/62) * 0.55);
      c.style.pointerEvents = isActive ? 'auto' : 'none';
    });
  }
  function animate(){
    const diff = target - angle;
    if(Math.abs(diff) < 0.06){ angle = target; render(); raf = null; return; }
    angle += diff * 0.18; render();
    raf = requestAnimationFrame(animate);
  }
  function go(){ if(reduce){ angle = target; render(); return; } if(!raf) raf = requestAnimationFrame(animate); }
  function snap(){ target = Math.round(target/STEP)*STEP; go(); }
  function step(dir){ target = Math.round(target/STEP)*STEP + dir*STEP; go(); }

  // wheel (scroll-jack acotado al stage)
  stage.addEventListener('wheel', e=>{
    if(stage.classList.contains('flat')) return;
    e.preventDefault();
    target += (e.deltaY>0?1:-1) * STEP * 0.5;
    clearTimeout(stage._snap); stage._snap = setTimeout(snap, 130); go();
  }, {passive:false});

  // drag
  let dragging=false, lastY=0, moved=false;
  stage.addEventListener('pointerdown', e=>{
    if(stage.classList.contains('flat')) return;
    dragging=true; moved=false; lastY=e.clientY; stage.classList.add('grabbing');
    try{ stage.setPointerCapture(e.pointerId); }catch(_){}
  });
  stage.addEventListener('pointermove', e=>{
    if(!dragging) return; const dy=e.clientY-lastY; if(Math.abs(dy)>2) moved=true; lastY=e.clientY;
    target += dy*0.35; go();
  });
  function endDrag(){ if(!dragging) return; dragging=false; stage.classList.remove('grabbing'); snap(); }
  stage.addEventListener('pointerup', endDrag);
  stage.addEventListener('pointercancel', endDrag);
  // evitar que un drag dispare click en los links de la card
  stage.addEventListener('click', e=>{ if(moved){ e.preventDefault(); moved=false; } }, true);

  // arrows + teclado
  stage.querySelector('.barrel-up')?.addEventListener('click', ()=>step(-1));
  stage.querySelector('.barrel-down')?.addEventListener('click', ()=>step(1));
  stage.addEventListener('keydown', e=>{
    if(e.key==='ArrowUp'){ e.preventDefault(); step(-1); }
    else if(e.key==='ArrowDown'){ e.preventDefault(); step(1); }
  });

  function setMode(){
    if(mq.matches || reduce){ stage.classList.add('flat'); barrel.style.transform=''; cards.forEach(c=>{c.style.transform='';c.style.opacity='';c.style.pointerEvents='';}); }
    else { stage.classList.remove('flat'); layout(); render(); }
  }
  setMode();
  (mq.addEventListener ? mq.addEventListener('change', setMode) : mq.addListener(setMode));
  window.addEventListener('resize', ()=>{ if(!stage.classList.contains('flat')){ layout(); render(); } });
})();
```

- [ ] **Step 3: Verificar (navegador)**

Desktop ≥1000px: la card del frente se ve grande, plana y legible; las vecinas difuminadas; `wheel` sobre el stage gira (no scrollea la página mientras el cursor está encima); arrastrar gira; suelta → snap; flechas ▲▼ giran ±1; loop entre las 9; solo la card activa es clickeable (su CTA abre el link). Consola sin errores. `applyLang` sigue andando (ES/EN/JP). Con `flat`/mobile: stack vertical, sin 3D, links OK.

- [ ] **Step 4: Commit**

```bash
git add index.html && git commit -m "feat(barrel): control 3D (drag/wheel/flechas/snap) y fallback"
```

---

### Task 6: Verificación final, README, review y merge

- [ ] **Step 1: Chequeos globales**

```bash
grep -c box-shadow index.html            # 0
grep -c 'data:image/jpeg' index.html      # 5 (screenshots)
grep -c 'barrel-card' index.html          # cards presentes
grep -c 'proj-hero\|filter-pill\|proj-grid\|proj-count' index.html  # 0 (viejo eliminado, HTML+CSS+JS)
```
Confirmar foto del hero intacta (186252 bytes) y `<script>` único.

- [ ] **Step 2: Verificación funcional completa (navegador, cache-bust)**

1440px: barril gira con drag/wheel/flechas, snap, activa interactiva, resto no; los 5 screenshots + 4 covers se ven; cada CTA abre el destino correcto (live vs GitHub); Smart Home sin link, con nota. 390px: stack vertical legible y clickeable. `prefers-reduced-motion`: sin animación de giro, usable. ES/EN/JP cambia toda la sección. Sin errores de consola. Sin scroll horizontal.

- [ ] **Step 3: README**

Actualizar "## Características": mencionar el carrusel 3D de proyectos ("barril") con screenshots, drag/scroll y fallback accesible.

- [ ] **Step 4: Commit + review final del branch**

```bash
git add index.html README.md && git commit -m "chore(barrel): README y verificacion final"
```
Correr el review final del branch (paquete saneado por el base64) antes de mergear.

- [ ] **Step 5: Merge + push** (con confirmación del usuario)

Fetch → integrar `origin/main` si divergió (como la vez pasada, sin forzar) → merge a `main` → push. Borrar la rama.

---

## Self-Review

**Cobertura del spec:**
- Cards uniformes horizontales mitad texto/mitad visual + botón grande → Task 2 (CSS) + Task 3 (HTML). ✓
- Screenshots (5) + covers (4) → Task 1 (captura) + Task 3 (covers) + Task 4 (inyección). ✓
- Barril 3D con control híbrido (drag/wheel/flechas/teclado) + snap + activa → Task 5. ✓
- Eliminar filtro/contador/tiers → Task 2 (CSS) + Task 3 (HTML) + Task 5 (JS). ✓
- Fallback mobile/reduce-motion → Task 2 (CSS `.flat` + `@media`) + Task 5 (setMode). ✓
- i18n ES/EN/JP en todo → Task 3 (data-*) + `applyLang` intacto (Task 5). ✓
- Sin box-shadow, foto base64 intacta, links exactos → verificado en cada tarea + Task 6. ✓

**Placeholders:** el HTML de las 9 cards se da como template + tabla de datos (copy reusado del HTML actual/catálogo) — para ejecución inline (el ejecutor tiene el copy a mano). Sin TBD en CSS/JS (completos).

**Consistencia de nombres:** `.barrel-stage#barrel`, `.barrel`, `.barrel-card`, `.bcard-*`, `.barrel-up/down`, `data-shot` (5 nombres: altoque/octava/dojo/guardarropas/monitor), `.flat` — usados igual en Tasks 2/3/4/5. `computeR/layout/render/animate/go/snap/step/setMode` definidos y usados dentro del mismo módulo. ✓
