# StockFlow · Dashboard administrativo de inventario

**Autor:** Andrés Jiménez

---

## 1. Descripción

**StockFlow** es un panel administrativo para la **gestión de inventario** de una tienda de tecnología.
En una sola pantalla reúne los indicadores operativos del mes, la evolución de ventas, la distribución
del stock por categoría, el listado de movimientos de bodega y el registro de actividad reciente.

La interfaz se inspira en los dashboards modernos publicados en **Dribbble** (colecciones *Admin
Dashboard* / *SaaS Analytics*), de los que se tomaron cinco patrones recurrentes:

| # | Componente | Patrón de referencia |
|---|------------|----------------------|
| 1 | **Barra lateral oscura y colapsable** | Menú vertical con iconos + etiqueta, indicador de sección activa y contador de alertas. |
| 2 | **Encabezado superior fijo** | Título de la vista, buscador global centrado, notificaciones y perfil de usuario. |
| 3 | **Tarjetas de resumen (KPI cards)** | Cuatro métricas con icono, valor grande y variación porcentual respecto al periodo anterior. |
| 4 | **Gráficos** | Gráfico de barras de ventas mensuales + gráfico circular (*donut*) con leyenda de categorías. |
| 5 | **Tabla de datos** | Filas con miniatura del producto, barra de existencias, insignias de estado y paginación. |

---

## 2. Tecnologías usadas

- **HTML5 semántico**: `header`, `aside`, `nav`, `main`, `section`, `article`, `table`, `figure`, `time`, `footer`.
- **CSS3**, sin frameworks ni JavaScript:
  - **CSS Grid** con `grid-template-areas` para el layout general y para el área principal.
  - **Flexbox** para todos los componentes internos (tarjetas, menú, cabecera, filas, leyenda, paginación).
  - **Custom properties** (`:root`) para colores, tipografías, radios, sombras y métricas del layout.
  - `conic-gradient` para el gráfico circular y gradientes lineales para las barras.
  - Transiciones, pseudo-clases (`:hover`, `:focus-visible`, `:focus-within`, `:checked`) y pseudo-elementos (`::before`).
  - **Media queries** para escritorio, portátil, tablet, móvil, además de `prefers-color-scheme`,
    `prefers-reduced-motion` y estilos de impresión.
- **Iconografía SVG** propia (`assets/`), sin dependencias externas.

### Estructura de archivos

```
panel-admin/
├── index.html          Estructura semántica del dashboard
├── styles.css          Layout (Grid), componentes (Flexbox), temas y responsividad
├── README.md           Este documento
├── assets/             Logotipo, avatar y miniaturas de producto en SVG
└── Evidencias/           Evidencias en escritorio, tablet, móvil y tema oscuro
```

---

## 3. Decisiones de diseño

**Layout con Grid y áreas nombradas.** El contenedor `.layout` declara cuatro regiones legibles:

```css
grid-template-areas:
  "sidebar header"
  "sidebar main"
  "sidebar footer";
```

Cambiar la disposición en cada breakpoint consiste solo en reescribir ese mapa, sin tocar el HTML.
El área principal usa una **segunda cuadrícula** (`tarjetas`, `grafico`, `circular`, `tabla`, `actividad`)
con la misma técnica.

**Flexbox para los componentes.** Grid resuelve la página; Flexbox resuelve el interior:
la fila de tarjetas (`flex-wrap` para que se reacomoden solas), el enlace del menú (icono + texto +
contador con `margin-left:auto`), la cabecera (título · buscador · acciones), la celda de producto
(miniatura + nombre + SKU), la barra de existencias, la leyenda y la paginación.

**Ancho del sidebar como variable.** `--w-sidebar` vive en `.layout`; el estado colapsado solo cambia
esa variable (`264px → 86px`) y el resto del layout se recalcula con una transición suave.
El interruptor es un `checkbox` oculto pero enfocable (`.nav-toggle:checked + .layout …`), de modo que
el menú colapsable funciona **sin una línea de JavaScript** y sigue siendo operable con teclado.

**Jerarquía visual.** Fondo neutro frío, superficies blancas con borde de 1 px y sombra suave, y un único
color de marca (índigo) reservado para la acción principal, la sección activa y la tarjeta destacada.
Los estados usan verde / ámbar / rojo, siempre acompañados de **texto**, nunca solo de color.

**Microinteracciones.** Elevación de tarjetas al pasar el cursor, giro leve del icono, barras del gráfico
que muestran su valor al enfocarlas, filas de tabla resaltadas, `:focus-within` en el buscador y
desplazamiento lateral de los enlaces del menú. Todas las transiciones se anulan con
`prefers-reduced-motion: reduce`.

**Responsividad (4 breakpoints).**

| Breakpoint | Comportamiento |
|------------|----------------|
| `≤ 1200 px` | El gráfico de barras ocupa el ancho completo; sidebar algo más estrecho. |
| `≤ 1024 px` | Sidebar siempre colapsado a iconos; contenido en una sola columna; tarjetas 2×2. |
| `≤ 768 px`  | El sidebar pasa a **menú superior horizontal**; el buscador salta a su propia línea. |
| `≤ 480 px`  | La tabla se convierte en fichas: `thead` se oculta y cada celda muestra su etiqueta con `content: attr(data-label)`. |

---

## 4. Accesibilidad

- **Landmarks y roles ARIA**: `role="banner"`, `role="navigation"`, `role="main"`, `role="search"`,
  `role="contentinfo"` y `role="group"` en los filtros del gráfico, junto con `aria-label` descriptivos.
- **Enlace de salto** (`.skip-link`) visible al recibir el foco, que lleva directamente a `#contenido`.
- **Foco siempre visible**: contorno ámbar de 3 px con `:focus-visible` en enlaces, botones, campos y
  etiquetas. El foco del checkbox oculto se refleja en el botón visible del menú.
- **Texto alternativo** en todas las imágenes (`alt` en logotipo, avatar y miniaturas de producto).
  Los SVG decorativos van con `aria-hidden="true"` y `focusable="false"` para no duplicar información.
- **Gráficos descritos**: el gráfico de barras y el circular usan `role="img"` con un `aria-label` que
  enumera los valores, de modo que un lector de pantalla obtiene los mismos datos que la vista.
- **Tabla accesible**: `caption` para lectores de pantalla, `scope="col"` en las cabeceras, `scope="row"`
  en el nombre de cada producto y un contenedor desplazable enfocable con `role="region"`.
- **Contraste (WCAG AA/AAA)**: texto principal `#10131f` sobre blanco ≈ 17:1; texto secundario `#545b70`
  ≈ 7:1; enlaces del sidebar `#a4adc9` sobre `#141a2e` ≈ 6,8:1; índigo de marca `#4338ca` sobre blanco
  ≈ 8,6:1. Ningún estado se comunica únicamente con color: las insignias incluyen texto.
- **Navegación por teclado** completa en el orden lógico: salto de contenido → colapsar menú → menú →
  buscador → acciones → contenido. `aria-current="page"` marca la sección y la página activas.
- **Preferencias del usuario** respetadas: tema oscuro (`prefers-color-scheme`) y reducción de
  movimiento (`prefers-reduced-motion`).

---

## 5. Cómo ejecutarlo

abrir `index.html`
