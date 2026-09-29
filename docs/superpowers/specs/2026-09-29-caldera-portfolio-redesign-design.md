# Rediseño del portfolio — estilo "Caldera" con colores propios

**Fecha:** 2026-09-29
**Autor:** Santino Vargas Di Buono (con Claude)
**Archivo objetivo:** `index.html` (único archivo, HTML/CSS/JS vanilla)

## Objetivo

Rediseñar el portfolio adoptando el **lenguaje visual del estilo "Caldera"** (refero.design)
—tipografía gigante, superficies planas sin sombras, esquinas de 40px, controles tipo
píldora, patrón halftone de puntos, divisores punteados— **pero usando la paleta de colores
actual** del sitio (crema/fuego/amarillo/cyan). Sumar **más vida y animación** con buen gusto,
y **reorganizar el layout** al estilo Caldera.

## Decisiones tomadas (brainstorming)

1. **Paleta:** colores propios actuales, con las *formas* de Caldera.
2. **Animación:** "con vida" — vivo pero prolijo (no máximo, no minimalista).
3. **Alcance:** rediseño de layout (se puede reorganizar/mover contenido).
4. **Foto del hero:** la foto va dentro del bloque halftone con tratamiento duotono +
   degradado cyan→fuego + puntos en una esquina.
5. **Proyectos = sección estrella.** Se traen los proyectos reales del GitHub; se muestran los
   **9 "serios"** (afuera Botonesmata y este portfolio). **AlToque** insignia, **Smart Home** destacado.
6. **Sin franja de stats grande** con contadores; se conservan las stats chicas de "Sobre mí".
7. **Nav con iconos de redes** (LinkedIn/GitHub) + **contacto oscuro con "input" tipo píldora** (mailto).

## Restricciones (no cambian)

- **Un solo archivo** `index.html`, sin build step, sin framework, deploy en Netlify.
- **Multi-idioma ES/EN/JP** vía atributos `data-es/data-en/data-jp` y el switcher JS actual: se conserva íntegro.
- **Todo el contenido actual** (textos, proyectos, links, foto) se mantiene; se puede reordenar.
- **Fuentes:** Bebas Neue (display) + DM Sans (body), ya cargadas por Google Fonts.
- **Accesibilidad:** respetar `prefers-reduced-motion`; contraste legible; navegación por teclado intacta.
- Sin backend: el contacto sigue siendo `mailto:` (el "input" del contacto es visual/decorativo o un mailto).

## Tokens de diseño

### Colores (rol Caldera → color propio)

| Rol | Token | Valor | Uso |
|---|---|---|---|
| Canvas (pumice) | `--canvas` | `#ede6d6` | Fondo de página (más oscuro que las cards) |
| Superficie (limestone) | `--surface` | `#f5f0e8` | Fondo de tarjetas (más claro → jerarquía sin sombra) |
| Superficie alt | `--surface-2` | `#e4dbc8` | Variante sutil / tags neutros |
| Ember (acento dominante) | `--fire` | `#e84e1b` | CTAs, stat cards, acentos clave |
| Ember claro | `--fire2` | `#ff7a3d` | Hover/acento secundario |
| Sulfur (tags) | `--yellow` | `#f5c800` | Badges/categorías píldora |
| Halftone base | `--cyan` | `#00c2cc` | Base del degradado halftone del hero + acento terciario |
| Halftone claro | `--cyan2` | `#5ee8f0` | Punta del degradado |
| Obsidian (texto) | `--ink` | `#1a1612` | Texto, títulos, secciones oscuras |
| Muted | `--muted` | `#7a6f60` | Texto secundario |
| Chalk | `--chalk` | `#ffffff` | Texto sobre superficies oscuras |

Regla: **jerarquía por color, no por sombra**. Canvas (más oscuro) → surface (más claro) → fire (acento).

### Tipografía

- **Bebas Neue** (display): line-height 0.94, tracking `+0.02em`.
  - Nombre hero: `clamp(72px, 13vw, 150px)`
  - Títulos de sección: `clamp(44px, 7vw, 90px)`
  - Número de stat: `clamp(48px, 6vw, 80px)`
  - Subtítulos de card: 26–36px
- **DM Sans 500** (body, siempre Medium): cuerpo 16px / lh 1.55; meta 12–14px.

### Formas y espaciado

- Radio cards: `40px` · píldoras: `800px` · inputs: `100px` · chico: `16px`.
- **Sin sombras** en ningún elemento (se elimina `box-shadow` actual; los hovers usan `transform`/`border`/color).
- Divisores **punteados** 1.5px en `--ink` (nav, skills, secciones, footer).
- Contenedor centrado **max-width 1280px**; gap de sección 80px; padding de card 40px; gap de elementos 16px.

## Layout nuevo (de arriba a abajo)

1. **Nav** — Logo izquierda (`Santino Vargas Di Buono.`). Items de navegación dentro de un
   **contenedor píldora limestone** (radio 800px). Idiomas (ES/EN/JP como píldoras) + iconos de
   redes a la derecha. Sticky, plano, borde inferior punteado.

2. **Hero** — Grid: izquierda el **nombre gigante** (Bebas ~150px, lh 0.94), tag píldora
   ("Disponible ahora" con dot pulsante), rol, descripción, **2 CTAs píldora** (ember relleno +
   outline). Derecha el **bloque halftone de 40px**: foto con tratamiento **duotono/halftone**,
   degradado `cyan→fire`, patrón de puntos que "respira" en una esquina. Las identity chips
   (título/agile/idiomas) quedan como **píldoras** debajo del bloque.

3. **Ticker** — Se conserva la marquesina (ya es muy on-brand). Re-estilo: barra `--ink`,
   items Bebas, dots de color. Bordes punteados arriba/abajo opcionales.

4. **Sobre mí** — Título de sección gigante. Texto editorial (DM Sans 500). Las identity cards
   pasan a **cards planas limestone 40px** con acento de color (borde/pill), sin sombra.
   `about-pills` → píldoras sulfur/ember/cyan. Se conservan las **stats chicas** actuales
   (cinturón negro · 3 idiomas · MMA) re-estiladas planas. Hover-lift por transform.
   *(Decisión: NO se agrega franja de stats grande con contadores.)*

5. **Skills** — Tira de 4 columnas separadas por **divisores punteados** (en vez de la grilla con
   bordes). Encabezados en Bebas con subrayado de color. Skills "fuertes" como **pills**; el resto
   como items con dot.

6. **Proyectos (SECCIÓN ESTRELLA)** — el foco del sitio. Se traen los **9 proyectos "serios"**
   desde el GitHub real (`santinovargasdb`) — se excluyen Botonesmata (fun) y este mismo portfolio.
   Layout en **3 tiers** para dar énfasis:
   - **Tier 1 — Insignia:** **AlToque** en card grande con **halftone cyan→fire** (equivalente al
     "plasma hero card" de Caldera). Título Bebas grande, stack completo, links GitHub + Live.
   - **Tier 2 — Destacado:** **Smart Home / Domótica ESP32** en card oscura (obsidiana) full-width
     con borde superior de gradiente — la historia de liderazgo (PM + dev frontend, equipo x5, IoT).
   - **Tier 3 — Grid filtrable:** **pills de categoría** (`Todos · Full-stack · IoT · Data & IA · Web`)
     que **filtran el grid con animación** (FLIP/transform + stagger). Cada card (limestone 40px, plana):
     tag de categoría (píldora amarilla) · título Bebas · descripción 1–2 líneas · tags de tech ·
     indicador **"En vivo"** (dot) + links GitHub / Demo · hover-lift plano.
   - Contador "N proyectos" arriba de la sección (único contador que sube al entrar en viewport).
   - Todo con hover-lift plano (transform + color), sin sombra.

7. **Contacto** — Sección **oscura (obsidiana)**, título gigante ("TRABAJAMOS JUNTOS?"),
   subtítulo, un **campo tipo píldora** (radio 100px, borde chalk) con botón ember que dispara
   `mailto`, y los contact links (LinkedIn/Email/GitHub) como **píldoras/cards planas** con iconos.

8. **Footer** — Plano, **divisor punteado** superior, © y los 3 dots de color.

## Inventario de animaciones ("con vida")

Todas condicionadas a `prefers-reduced-motion: no-preference`.

1. **Hero reveal escalonado** — nombre por líneas, luego tag/rol/desc/CTAs con delay incremental.
2. **Halftone vivo** — patrón de puntos (radial-gradients) con drift/pulse sutil + shift lento del degradado.
3. **Botones magnéticos** — el botón se desplaza levemente hacia el cursor en hover (JS).
4. **Contador de proyectos** — el "N proyectos" del header de Proyectos sube desde 0 al entrar en viewport (IntersectionObserver). (No hay franja de stats.)
5. **Hover-lift en cards** — `translateY(-4px)` + cambio de borde/color/escala; **nunca sombra**.
6. **Scroll reveal** — se mantiene el IntersectionObserver actual, con stagger refinado.
7. **Reveal de títulos** — títulos de sección con slide/clip-wipe al entrar.
8. **Parallax sutil** — bloque halftone y blobs se mueven levemente con scroll/mouse.
9. **Ticker** — se conserva.
10. **Cursor glow** — se conserva (tint acorde), oculto en touch.

## Qué se conserva vs. qué cambia

**Se conserva:** contenido, textos multi-idioma, switcher ES/EN/JP, links, foto, ticker,
scroll-reveal, cursor glow, fuentes.

**Cambia:** paleta reasignada a roles Caldera, radios (→40px/pill), eliminación de sombras,
contenedor con max-width, tipografía a escala arquitectónica, nav en píldora (con iconos de
redes), hero con bloque halftone, **sección de proyectos como estrella (3 tiers + grid filtrable)**,
restyle de todas las cards, sección de contacto oscura,
divisores punteados, y las animaciones nuevas del inventario.

## No-goals (YAGNI)

- Sin framework, bundler ni build step. Sigue siendo un `index.html`.
- Sin backend ni envío real de formularios (contacto = mailto).
- Sin secciones de contenido nuevas más allá de la franja de stats.
- Sin cambiar textos/copys de fondo (solo ajustes mínimos para stats).
- Sin librerías JS externas de animación (todo vanilla + CSS).

## Apéndice — Catálogo de proyectos (fuente de verdad, GitHub real)

Datos traídos de `github.com/santinovargasdb` (2026-09-29). Copy trilingüe ES/EN/JP se
escribe en implementación; acá va el dato base. Tier: 1=insignia, 2=destacado, G=grid.

| # | Proyecto | Tier | Categoría | Descripción base (ES) | Stack | GitHub | Live |
|---|---|---|---|---|---|---|---|
| 1 | **AlToque** | 1 | Full-stack | Marketplace que conecta personas con profesionales de oficios verificados (plomeros, electricistas, cerrajeros) para urgencias y trabajos agendados. Matching por geolocalización, pago híbrido con Mercado Pago, instalable como PWA. | Next.js 15 · TypeScript · Supabase · PostGIS · Mercado Pago · Tailwind v4 · PWA | `AlToque` | al-toque-eta.vercel.app |
| 2 | **Smart Home — Domótica** | 2 | IoT | Maqueta a escala de una smart home funcional con ESP32 por WiFi a una interfaz web: control de servomotores, LEDs y sensores en tiempo real. Lideré un equipo de 5 como PM y dev frontend. | ESP32 · HTML/CSS · WiFi · IoT · XAMPP · Team Lead x5 | — (proyecto escolar) | — |
| 3 | **LogiSwift** | G | Full-stack | PWA mobile-first de logística urbana para un repartidor/vendedor: hoja de ruta del día, registro de ventas en el momento, stock del vehículo y cierre de jornada. Diseñada para usarse con una mano arriba de la camioneta. | Vite · React 19 · TypeScript · Tailwind v4 · shadcn/ui · TanStack Query · Supabase · PWA | `LogiSwift` | — |
| 4 | **Octava Café** (Virtual-Kiosk) | G | Full-stack | Cafetería de especialidad en modalidad Take Away: catálogo online, personalización del pedido (leche, azúcar, Sin TACC), elección de horario de retiro y pago con Mercado Pago (Checkout Pro + Webhooks IPN). | PHP 8 · MySQLi · JS Vanilla (ES Modules) · MySQL · Mercado Pago (IPN) | `Virtual-Kiosk` | virtual-kiosk.vercel.app |
| 5 | **Dojo Ledger** (habits-tracker) | G | Full-stack | PWA mobile-first para trackear hábitos con una economía de monedas gamificada: control de 3 estados a un toque, actualizaciones optimistas y balance en vivo. | Next.js 16 · React 19 · Tailwind v4 · shadcn/ui · Supabase | `habits-tracker-app` | habits-tracker-app-ten.vercel.app |
| 6 | **Arcea** (arcea-app) | G | Full-stack | Tienda online completa: catálogo de productos, carrito y checkout con Mercado Pago, autenticación de usuarios y SEO técnico (sitemap, OpenGraph). | Next.js 15 · TypeScript · Supabase · Mercado Pago · shadcn/ui | `arcea-app` | — |
| 7 | **Guardarropas Virtual** (virtual-wardrobe-app) | G | Full-stack · IA | App para digitalizar tu guardarropa: subís tus prendas, las organizás en un clóset virtual y generás combinaciones de outfits con un estilista por IA. | React · TypeScript · Vite · Supabase · IA | `Guardarropas-Virtual` | guardarropas-virtual.vercel.app |
| 8 | **Monitor de Medios SMATA** (social-media-filter-engine) | G | Data & IA | Solución real para una empresa real: monitor de medios y redes para el Depto. de Prensa de SMATA, con filtrado por reglas, scoring por IA (Gemini) e informes en Word. | Python · Filtrado img/texto · IA (Gemini) · Reportes Word | `social-media-filter-engine` | filtro-redes-sociales-smt.vercel.app |
| 9 | **Inteligencia de Noticias** (monitor-inteligencia-smata) | G | Data & IA | Sistema modular en Python que extrae noticias por RSS, las categoriza y resume con NLP, traduce fuentes internacionales y genera informes ejecutivos. Interfaz en Streamlit. | Python · RSS · NLP · TextBlob · Streamlit | `monitor-inteligencia-smata` | — |
| 10 | **Botonesmata** (botonera-app) | G | Web · Fun | Botonera de sonidos tipo sampler: subís o grabás audios con el micrófono, se guardan en el navegador (IndexedDB) y sobreviven al recargar. Sin servidor. | JavaScript Vanilla · IndexedDB · Web Audio · MediaRecorder | `botonera-app` | — |
| 11 | **Este sitio** (portfolio-web) | G | Web | Portfolio personal con soporte ES/EN/JP, diseñado y desarrollado desde cero. | HTML · CSS · JavaScript vanilla | `portfolio-web` | santinovargasdb.netlify.app |

Notas:
- El repo `santinovargasdb` (perfil README) se omite.
- **Se muestran 9 proyectos "serios" (#1–#9).** Se **excluyen** del sitio: #10 Botonesmata (fun)
  y #11 este portfolio. Quedan en el catálogo solo como referencia.
- Grid filtrable = #3–#9 (AlToque es tier 1, Smart Home tier 2).
- Categorías del filtro: `Todos · Full-stack · IoT · Data & IA · Web`.
- Los links de GitHub son `github.com/santinovargasdb/<repo>`.

## Verificación

- Abrir en navegador a ~1440px, ~768px y ~380px: todas las secciones renderizan y son legibles.
- Cambio de idioma ES/EN/JP sigue funcionando en todo el contenido nuevo.
- Contadores, reveals, halftone y hovers disparan; con `prefers-reduced-motion` quedan estáticos y usables.
- Sin errores en consola. Todos los links externos siguen apuntando a destinos correctos.
- Sin `box-shadow` en el CSS final (grep de control).
