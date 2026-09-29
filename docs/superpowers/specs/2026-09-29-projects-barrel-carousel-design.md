# Sección de Proyectos — Barril de revólver (carrusel vertical 3D)

**Fecha:** 2026-09-29
**Autor:** Santino Vargas Di Buono (con Claude)
**Archivo objetivo:** `index.html` (único archivo, HTML/CSS/JS vanilla)

## Objetivo

Rediseñar la sección `#proyectos` para que:
1. Todas las cards sean **uniformes y horizontales** (se elimina la separación tier1/tier2/grid).
2. Cada card sea **mitad texto/explicación + mitad visual interactuable** (screenshot o cover) con un **botón grande** que lleva al deploy o al GitHub.
3. Las 9 cards vivan en un **carrusel vertical 3D estilo barril de revólver**, que gira con el mouse (scroll + arrastrar).

## Decisiones tomadas (brainstorming)

1. **Control del giro:** híbrido — arrastrar vertical (primario) **y** scroll con el cursor sobre el barril; + flechas ▲▼. Snap a la card más cercana.
2. **Visual de la card:** **screenshot real** en los desplegados + **cover diseñado on-brand** (gradiente/halftone + nombre + badges) en los no desplegados. Overlay on-brand en ambos.
3. **Se elimina el filtro de categorías y el contador.** La categoría queda como tag por card.
4. **Alcance:** las mismas 9 cards actuales (no se re-agregan Botonesmata ni el portfolio).
5. **Botón grande** por card: "Ver en vivo →" si hay deploy, si no "Ver en GitHub →"; + link secundario a GitHub cuando el primario es el deploy.
6. **Fallback** mobile/touch y `prefers-reduced-motion`: se apaga el 3D → stack vertical de las mismas cards (imagen arriba, texto abajo), todo legible y clickeable.

## Restricciones (no cambian)

- **Un solo archivo** `index.html`, sin build, sin framework, sin librerías JS externas. Deploy en Netlify.
- **Multi-idioma ES/EN/JP** con `data-es/en/jp`: se conserva; todo texto nuevo lleva los 3.
- **Foto base64 del hero** intacta (186252 bytes) — no se toca.
- **Sin `box-shadow`** (diseño plano). Radios 40px cards / 800px pills. Bebas display + DM Sans 500.
- Se respeta `prefers-reduced-motion`.
- Links externos exactos (GitHub `github.com/santinovargasdb/<repo>`, demos Vercel).

## Catálogo de las 9 cards (fuente de verdad)

| # | Proyecto | Categoría | Deploy | Visual | GitHub repo | Live |
|---|---|---|---|---|---|---|
| 1 | AlToque | Full-stack | sí | **screenshot** | AlToque | al-toque-eta.vercel.app |
| 2 | Octava Café (Virtual-Kiosk) | Full-stack | sí | **screenshot** | Virtual-Kiosk | virtual-kiosk.vercel.app |
| 3 | Dojo Ledger (habits-tracker-app) | Full-stack | sí | **screenshot** | habits-tracker-app | habits-tracker-app-ten.vercel.app |
| 4 | Guardarropas Virtual | Full-stack · IA | sí | **screenshot** | Guardarropas-Virtual | guardarropas-virtual.vercel.app |
| 5 | Monitor de Medios (social-media-filter-engine) | Data & IA | sí | **screenshot** | social-media-filter-engine | filtro-redes-sociales-smt.vercel.app |
| 6 | LogiSwift | Full-stack | no | **cover** | LogiSwift | — |
| 7 | Arcea (arcea-app) | Full-stack | no | **cover** | arcea-app | — |
| 8 | Inteligencia de Noticias (monitor-inteligencia-smata) | Data & IA | no | **cover** | monitor-inteligencia-smata | — |
| 9 | Smart Home — Domótica | IoT | no (proyecto escolar) | **cover** | — (sin repo) | — |

Orden inicial en el barril: fila de arriba (los desplegados con screenshot primero), pero como es un loop el orden es solo la posición de inicio. AlToque arranca al frente.

Copy ES/EN/JP y tags de tech: se reutilizan los textos actuales de cada proyecto (ya en el HTML). Smart Home no tiene botón de link (etiqueta "Proyecto escolar · 2024").

## La card uniforme

Estructura (grid 2 columnas dentro de la card, ~1fr/1fr):

- **Izquierda (texto):**
  - Tag de categoría (píldora sulfur).
  - Título Bebas (~40px al frente).
  - Descripción (DM Sans 500, `data-*`).
  - Tags de tecnología (píldoras).
  - **CTA grande** (`.btn-fire` estilo, ~grande): `Ver en vivo →` (deploy) o `Ver en GitHub →`. + link secundario `GitHub` (icono) cuando el primario es el deploy.
- **Derecha (visual):**
  - Panel de 40px radio con el **screenshot** (`<img>` con data-URI JPEG) o el **cover diseñado** (gradiente cyan→fire + halftone + inicial/nombre + badges de tech).
  - Overlay: degradado sutil en el borde + badge "En vivo" (píldora) en los desplegados.
  - Todo el panel es un `<a>` clickeable al deploy (o al GitHub si no hay deploy).

Card: fondo `--surface`, radio 40px, plana. Ancho ~ contenedor (max ~1100px), alto ~clamp(300px, 42vh, 360px).

## Mecánica del barril (CSS 3D + JS vanilla)

**Geometría:**
- Contenedor `.barrel-stage`: `perspective: ~1200px`, alto fijo (deja ver la card del frente + un asomo de las vecinas).
- `.barrel`: `transform-style: preserve-3d`, rota en X: `transform: rotateX(var(--angle))`.
- 9 `.barrel-card`, cada una faceta del cilindro: `transform: rotateX(i*40deg) translateZ(RADIUS)`. Con 9 cards → 40° por faceta.
- `RADIUS` ≈ `(cardHeight/2) / tan(20°)` para que las facetas no se solapen (≈ 460px para card de ~340px). Se ajusta en implementación.
- `backface-visibility: hidden` en las cards; opacidad/nitidez en función del ángulo (front = 1; se difuminan hacia los costados).

**Card activa:**
- La faceta cuyo ángulo neto ≈ 0 es la **activa**: opacidad 1, escala 1, **interactiva** (`pointer-events` on).
- Las demás: opacidad reducida, `pointer-events: none` (no se clickean por error). Backface oculta.
- Al girar, se recalcula cuál es la activa (índice = `round(-angle/40) mod 9`).

**Control (híbrido):**
- **Drag:** `pointerdown`+`pointermove` vertical sobre el stage → suma al ángulo objetivo (dirección = movimiento del mouse). `pointerup` → snap.
- **Wheel:** `wheel` sobre el stage → suma al ángulo objetivo; `preventDefault()` mientras el cursor está sobre el stage (scroll-jacking acotado; se sale moviendo el cursor afuera).
- **Flechas ▲▼** (botones visibles) → ±1 card, con snap.
- **Teclado:** cuando el stage tiene foco, `ArrowUp/ArrowDown` giran ±1 card (accesibilidad).
- **Snap:** al soltar/idle, anima el ángulo al múltiplo de 40° más cercano (easing). Animación por `requestAnimationFrame` (lerp del ángulo actual → objetivo).
- **Loop infinito:** el ángulo no se clampea; la card activa se calcula módulo 9.

**Indicadores:** puntos/índice al costado ("3 / 9") opcional, y las flechas.

## Screenshots

- Se capturan los **5 sitios desplegados** con el navegador (viewport ~1280×800), se recortan/redimensionan a ~800–900px de ancho y se **comprimen a JPEG** (calidad ~0.72) para bajar peso.
- Se embeben como `data:image/jpeg;base64,...` en el `<img>` de cada card desplegada.
- Peso estimado: ~80–140KB por imagen × 5 ≈ **~400–600KB** sumados al `index.html` (aceptable para un sitio estático en Netlify).
- Los **4 no desplegados** usan cover diseñado (sin imagen) → 0 peso extra.
- `loading="lazy"` en los `<img>` para no penalizar la carga inicial.

## Fallback (mobile / reduced-motion)

- **`@media (max-width: 900px)` o touch:** se desactiva el 3D. `.barrel` pasa a un stack vertical normal; cada card se apila (visual arriba, texto abajo), scroll natural de la página, todas interactivas.
- **`prefers-reduced-motion: reduce`:** sin giro animado ni tilt; se muestra como stack vertical (o carrusel simple con las flechas), todo usable. La snap-animation se reemplaza por salto directo.

## Qué se elimina / reemplaza

- Se elimina el HTML y CSS de: `.proj-hero` (tier1), `.proj-dark` (tier2), `.filter-bar`/`.filter-pill`, `.proj-grid`, `.proj-card`, `.cat-tag`, `.proj-count`, `.live-dot` (se rehace como badge).
- Se elimina el **JS del filtro y del contador** (`.filter-pill` handlers, `.proj-count` count-up).
- Se agrega: CSS del barril + cards uniformes + covers, y JS del barril (drag/wheel/arrows/snap/active).
- Todo lo demás del sitio queda igual.

## No-goals (YAGNI)

- Sin framework, bundler ni librerías 3D (todo CSS 3D + JS vanilla).
- Sin iframes en vivo (bloqueos de embed + peso).
- Sin re-agregar proyectos excluidos (Botonesmata, portfolio).
- Sin backend.

## Verificación

- Desktop (~1440px): el barril gira con drag y con wheel-sobre-el-stage; snap a card; la card del frente es legible e interactiva; las demás difuminadas y no clickeables; flechas ▲▼ funcionan; loop correcto entre las 9.
- Los 5 screenshots se ven; los 4 covers se ven on-brand; cada card tiene el botón grande correcto (deploy vs GitHub); links correctos.
- Cambio de idioma ES/EN/JP actualiza toda la sección nueva.
- Mobile (~390px) y `prefers-reduced-motion`: stack vertical legible y clickeable, sin 3D, sin scroll atrapado.
- Sin `box-shadow` (grep = 0). Foto base64 intacta (186252 bytes). Sin errores de consola.
