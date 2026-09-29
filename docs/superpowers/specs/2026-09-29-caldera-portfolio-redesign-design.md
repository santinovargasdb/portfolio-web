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

4. **Franja de stats (NUEVA — stat cards firma de Caldera)** — Fila de 4 cards de 40px, una
   ember y las otras en surface/cyan/yellow, con **números gigantes Bebas que suben con contador**
   al entrar en viewport. Contenido honesto y contable:
   - `3` Idiomas · `7` Proyectos · `5` Personas lideradas · `12` Años en artes marciales.
   (Copys finales ajustables; los números salen del contenido real.)

5. **Sobre mí** — Título de sección gigante. Texto editorial (DM Sans 500). Las identity cards
   pasan a **cards planas limestone 40px** con acento de color (borde/pill), sin sombra.
   `about-pills` → píldoras sulfur/ember/cyan. Hover-lift por transform.

6. **Skills** — Tira de 4 columnas separadas por **divisores punteados** (en vez de la grilla con
   bordes). Encabezados en Bebas con subrayado de color. Skills "fuertes" como **pills**; el resto
   como items con dot.

7. **Proyectos**
   - **AlToque** → card destacada grande con **halftone cyan→fire** (equivalente al "plasma hero card").
   - **Smart Home** → card oscura destacada (obsidiana) full-width con borde superior de gradiente.
   - **Grid de content cards** (Arcea, Guardarropas, Inteligencia de Noticias, Monitor de Medios,
     Este sitio): cards limestone 40px, **tag de categoría amarillo píldora**, título Bebas, links.
   - Todos con hover-lift plano (transform + color), sin sombra.

8. **Contacto** — Sección **oscura (obsidiana)**, título gigante ("TRABAJAMOS JUNTOS?"),
   subtítulo, un **campo tipo píldora** (radio 100px, borde chalk) con botón ember (mailto),
   y los contact links como **píldoras/cards planas** con iconos.

9. **Footer** — Plano, **divisor punteado** superior, © y los 3 dots de color.

## Inventario de animaciones ("con vida")

Todas condicionadas a `prefers-reduced-motion: no-preference`.

1. **Hero reveal escalonado** — nombre por líneas, luego tag/rol/desc/CTAs con delay incremental.
2. **Halftone vivo** — patrón de puntos (radial-gradients) con drift/pulse sutil + shift lento del degradado.
3. **Botones magnéticos** — el botón se desplaza levemente hacia el cursor en hover (JS).
4. **Contadores** — los números de la franja de stats suben desde 0 al entrar en viewport (IntersectionObserver).
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
contenedor con max-width, tipografía a escala arquitectónica, nav en píldora, hero con bloque
halftone, franja de stats nueva, restyle de todas las cards, sección de contacto oscura,
divisores punteados, y las animaciones nuevas del inventario.

## No-goals (YAGNI)

- Sin framework, bundler ni build step. Sigue siendo un `index.html`.
- Sin backend ni envío real de formularios (contacto = mailto).
- Sin secciones de contenido nuevas más allá de la franja de stats.
- Sin cambiar textos/copys de fondo (solo ajustes mínimos para stats).
- Sin librerías JS externas de animación (todo vanilla + CSS).

## Verificación

- Abrir en navegador a ~1440px, ~768px y ~380px: todas las secciones renderizan y son legibles.
- Cambio de idioma ES/EN/JP sigue funcionando en todo el contenido nuevo.
- Contadores, reveals, halftone y hovers disparan; con `prefers-reduced-motion` quedan estáticos y usables.
- Sin errores en consola. Todos los links externos siguen apuntando a destinos correctos.
- Sin `box-shadow` en el CSS final (grep de control).
