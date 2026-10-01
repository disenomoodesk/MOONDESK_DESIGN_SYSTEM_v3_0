# MoonDesk Design System v3.0

Sistema de diseño basado en Material 3, optimizado para consistencia, accesibilidad y alineación diseño–desarrollo.

**Stack:** Angular Material 19 (M3)
**Fuente de verdad:** Figma — DS Foundations & Components
**Tipografía:** Inter

> **v3.0 — nota de versión:** esta versión corrige errores detectados en la auditoría de septiembre 2026 contra Angular Material 19 y WCAG 2.1 AA, y ya incorpora las dos decisiones de equipo que quedaban pendientes (tonos de warning/success y peso tipográfico de headlines). Ver el [Changelog](#changelog-v20--v30) al final del documento para el detalle completo de qué cambió y por qué.

---

## Índice

1. [Arquitectura del sistema](#1-arquitectura-del-sistema)
2. [Design Tokens — Primitivos](#2-design-tokens--primitivos)
3. [Design Tokens — Semánticos](#3-design-tokens--semánticos)
4. [Tipografía](#4-tipografía)
5. [Espaciado](#5-espaciado)
6. [Border Radius](#6-border-radius)
7. [Elevación](#7-elevación)
8. [Variables CSS](#8-variables-css)
9. [Integración Angular Material](#9-integración-angular-material)
10. [Componentes — Diálogos](#10-componentes--diálogos)
11. [Componentes pendientes de documentar](#11-componentes-pendientes-de-documentar)
12. [Guidelines UX](#12-guidelines-ux)
13. [Gobernanza](#13-gobernanza)
14. [Changelog v2.0 → v3.0](#changelog-v20--v30)

---

## 1. Arquitectura del sistema

MoonDesk se estructura en tres capas. Cada capa solo puede consumir de la inmediatamente anterior.

```
Base Tokens (primitivos)
        ↓
Semantic Tokens (intención de uso)
        ↓
Component Tokens (implementación UI)
```

> **Regla fundamental:** ningún componente consume tokens primitivos directamente. Siempre se usa el token semántico correspondiente.

> **Regla de integración (nueva en v3.0):** los tokens semánticos (`--color-*`) deben mapear 1:1 con las variables de sistema que genera Angular Material 19 (`--mat-sys-*`) al aplicar el tema M3. Ver Sección 9 — si este mapeo no existe, el sistema de tokens documentado aquí y lo que efectivamente se renderiza en la app quedan desincronizados.

---

## 2. Design Tokens — Primitivos

Los primitivos son los valores brutos de la paleta. No se usan directamente en componentes.

### Paleta de color

```json
{
  "color": {
    "primary": {
      "0":   "#000000",
      "10":  "#001D36",
      "20":  "#00315A",
      "30":  "#00497E",
      "40":  "#0061A6",
      "50":  "#0079CC",
      "60":  "#2D91E3",
      "70":  "#59A9F8",
      "80":  "#A0CAFF",
      "90":  "#D2E4FF",
      "95":  "#EAF1FF",
      "99":  "#FDFCFF",
      "100": "#FFFFFF"
    },
    "secondary": {
      "0":   "#000000",
      "10":  "#0D1D2E",
      "20":  "#223244",
      "30":  "#36485F",
      "40":  "#4E6078",
      "50":  "#667892",
      "60":  "#7F92AD",
      "70":  "#99ADC8",
      "80":  "#B4C8E4",
      "90":  "#CCDDF7",
      "95":  "#DDEEFF",
      "100": "#FFFFFF"
    },
    "tertiary": {
      "0":   "#000000",
      "10":  "#00363B",
      "20":  "#004F56",
      "30":  "#006972",
      "40":  "#00838F",
      "50":  "#009FAD",
      "60":  "#00BCD4",
      "70":  "#4DD9EF",
      "80":  "#80DEEA",
      "90":  "#B2EBF2",
      "95":  "#E0F7FA",
      "100": "#FFFFFF"
    },
    "neutral": {
      "0":   "#000000",
      "10":  "#181C22",
      "20":  "#2D3038",
      "30":  "#43474F",
      "40":  "#5A5F66",
      "50":  "#73777F",
      "60":  "#8D9199",
      "70":  "#A8ABB3",
      "80":  "#C3C7CF",
      "87":  "#D8DBE1",
      "90":  "#D5D8E3",
      "92":  "#DDE0EA",
      "94":  "#E6E7F0",
      "95":  "#EFF0F7",
      "96":  "#EDEEF7",
      "98":  "#F7F8FF",
      "100": "#FFFFFF"
    },
    "error": {
      "40":  "#A83445",
      "50":  "#C94C5C",
      "95":  "#FFECEC",
      "100": "#FFFFFF"
    },
    "orange": {
      "30":  "#935E1E",
      "40":  "#D58B32",
      "50":  "#EEAD5F",
      "95":  "#FDF5EB",
      "100": "#FFFFFF"
    },
    "green": {
      "30":  "#317534",
      "40":  "#43A047",
      "60":  "#66BB6A",
      "95":  "#E8F5E9",
      "100": "#FFFFFF"
    }
  }
}
```

> **Corregido en v3.0:** `neutral-100` era `#F2F5FA` (más oscuro que `neutral-96` y que `neutral-98`), lo cual rompía la regla de que el tono 100 siempre es el blanco puro de la escala. Se corrigió a `#FFFFFF`, igual que en el resto de las paletas.
>
> **Resuelto en v3.0:** se agregó el stop `orange-30` (`#935E1E`) y `green-30` (`#317534`). Ninguno de los tonos que ya existían en esas dos paletas alcanzaba 4.5:1 de contraste contra su respectivo `-container` (95) — ni siquiera el 40, que era el más oscuro disponible. Se calcularon estos dos tonos nuevos manteniendo el mismo matiz y saturación, oscurecidos hasta superar 5:1 con margen. Se llaman "-30" (y no "-20") porque solo necesitaban ser más oscuros que el 40 existente, no igualar la luminosidad absoluta de otras familias de color — cada familia tiene su propia curva de tono. El equipo de diseño puede ajustar el matiz exacto en Figma siempre que mantenga ≥4.5:1 contra `orange-95` / `green-95`.

---

## 3. Design Tokens — Semánticos

Los tokens semánticos expresan intención de uso. Son el contrato entre diseño y desarrollo.

### 3.1 Color — Primarios

| Token | Primitivo | Valor | Uso |
|---|---|---|---|
| `color-primary` | primary-30 | `#00497E` | Acción principal, CTA, elementos de marca |
| `color-on-primary` | primary-100 | `#FFFFFF` | Texto/icono sobre fondo primary |
| `color-primary-container` | primary-95 | `#EAF1FF` | Fondos de chips, badges, estados activos suaves |
| `color-on-primary-container` | primary-40 | `#0061A6` | Texto/icono sobre primary container |
| `color-inverse-primary` | primary-80 | `#A0CAFF` | Acciones primarias sobre superficies inversas |

*Contraste verificado: `on-primary-container` sobre `primary-container` = 5.68:1 ✅*

### 3.2 Color — Secundarios

| Token | Primitivo | Valor | Uso |
|---|---|---|---|
| `color-secondary` | secondary-40 | `#4E6078` | Acciones secundarias, elementos de soporte |
| `color-on-secondary` | secondary-100 | `#FFFFFF` | Texto/icono sobre fondo secondary |
| `color-secondary-container` | secondary-90 | `#CCDDF7` | Fondos de elementos secundarios activos |
| `color-on-secondary-container` | secondary-30 | `#36485F` | Texto/icono sobre secondary container |

*Contraste verificado: `on-secondary-container` sobre `secondary-container` = 6.78:1 ✅*

### 3.3 Color — Terciarios

| Token | Primitivo | Valor | Uso |
|---|---|---|---|
| `color-tertiary` | tertiary-40 | `#00838F` | Acentos, elementos complementarios |
| `color-on-tertiary` | tertiary-100 | `#FFFFFF` | Texto/icono sobre fondo tertiary |
| `color-tertiary-container` | tertiary-95 | `#E0F7FA` | Fondos de elementos terciarios |
| `color-on-tertiary-container` | ~~tertiary-50~~ **tertiary-30** | ~~`#00ACC1`~~ **`#006972`** | Texto/icono sobre tertiary container |

> **Corregido en v3.0:** `on-tertiary-container` usaba tertiary-50 (`#00ACC1`), con un contraste de solo 2.46:1 contra su container — fallaba WCAG AA. Se corrigió a tertiary-30 (`#006972`), que da 5.78:1 ✅.

### 3.4 Color — Error

| Token | Primitivo | Valor | Uso |
|---|---|---|---|
| `color-error` | error-40 | `#A83445` | Estados de error, validaciones fallidas |
| `color-on-error` | error-100 | `#FFFFFF` | Texto/icono sobre fondo error |
| `color-error-container` | error-95 | `#FFECEC` | Fondo de mensajes de error, alertas inline |
| `color-on-error-container` | ~~error-50~~ **error-40** | ~~`#C94C5C`~~ **`#A83445`** | Texto/icono sobre error container |

> **Corregido en v3.0:** `on-error-container` usaba error-50 (`#C94C5C`), con 3.95:1 de contraste — solo pasa para texto grande, no para texto normal. Se corrigió a error-40 (`#A83445`, el mismo valor que `color-error`), que da 5.69:1 ✅.

### 3.5 Color — Superficies

| Token | Primitivo | Valor | Uso |
|---|---|---|---|
| `color-surface-dim` | neutral-87 | `#D8DBE1` | Superficie atenuada, fondos desactivados |
| `color-surface` | neutral-98 | `#F7F8FF` | Superficie base de la app |
| `color-surface-bright` | neutral-100 | `#FFFFFF` | Superficie luminosa, cards destacadas |
| `color-surface-container-lowest` | neutral-100 | `#FFFFFF` | Contenedores de menor jerarquía |
| `color-surface-container-low` | neutral-96 | `#EDEEF7` | Contenedores bajos |
| `color-surface-container` | neutral-94 | `#E6E7F0` | Contenedores estándar |
| `color-surface-container-high` | neutral-92 | `#DDE0EA` | Contenedores con mayor énfasis |
| `color-surface-container-highest` | neutral-90 | `#D5D8E3` | Contenedores de mayor jerarquía visual |

> **Corregido en v3.0:** `surface-container-lowest` dependía de `neutral-100`, que antes era más oscuro que `neutral-98` (ver Sección 2). Con la corrección del primitivo, este token ahora sí es el más claro de la escala de superficies, como corresponde a su nombre.

### 3.6 Color — Texto y contorno

| Token | Primitivo | Valor | Uso |
|---|---|---|---|
| `color-on-surface` | neutral-10 | `#181C22` | Texto principal sobre superficies |
| `color-on-surface-variant` | neutral-40 | `#5A5F66` | Texto secundario, placeholders |
| `color-outline` | neutral-50 | `#73777F` | Bordes de inputs, separadores visibles |
| `color-outline-variant` | neutral-70 | `#A8ABB3` | Separadores sutiles, divisores |

### 3.7 Color — Inversas y especiales

| Token | Primitivo | Valor | Uso |
|---|---|---|---|
| `color-inverse-surface` | neutral-20 | `#2D3038` | Tooltips, snackbars, fondos invertidos |
| `color-inverse-on-surface` | neutral-95 | `#EFF0F7` | Texto sobre inverse surface |
| `color-scrim` | neutral-10 @ 0.32 | `rgba(24,28,34,0.32)` | Overlay de modales y diálogos |
| `color-shadow` | neutral-10 | `#181C22` | Base para sombras de elevación |

### 3.8 Color — Estados semánticos

| Token | Primitivo | Valor | Uso |
|---|---|---|---|
| `color-warning` | orange-40 | `#D58B32` | Alertas, advertencias |
| `color-warning-container` | orange-95 | `#FDF5EB` | Fondo de warnings |
| `color-on-warning-container` | ~~orange-50~~ **orange-30** | ~~`#EEAD5F`~~ **`#935E1E`** | Texto sobre warning container |
| `color-success` | green-40 | `#43A047` | Confirmaciones, estados de éxito |
| `color-success-container` | green-95 | `#E8F5E9` | Fondo de estados exitosos |
| `color-on-success-container` | ~~green-60~~ **green-30** | ~~`#66BB6A`~~ **`#317534`** | Texto sobre success container |

> **Corregido en v3.0:** los valores anteriores (orange-50: 1.80:1, green-60: 2.10:1) fallaban gravemente el contraste, y ni siquiera el tono más oscuro que ya existía en cada paleta (orange-40, green-40) alcanzaba 4.5:1. Se agregó un nuevo primitivo más oscuro en cada familia (orange-30, green-30 — ver Sección 2) y se lo usó como `on-container`. Resultado verificado: `on-warning-container` 5.03:1 ✅, `on-success-container` 5.01:1 ✅ (ambos con margen sobre el mínimo de 4.5:1, para no quedar al borde).

---

## 4. Tipografía

**Familia:** Inter — Google Fonts / self-hosted
**Sistema base:** escala M3 adaptada

| Token | Tamaño | Line height | Peso | Uso |
|---|---|---|---|---|
| `typography-display-small` | 36px | 44px | 700 | Títulos hero, pantallas vacías |
| `typography-headline-large` | 32px | 40px | 700 | Encabezados principales de sección |
| `typography-headline-medium` | 28px | 36px | 700 | Encabezados secundarios |
| `typography-headline-small` | 24px | 32px | 700 | Títulos de diálogos, paneles |
| `typography-title-large` | 22px | 32px | 400 | Títulos de tarjetas |
| `typography-title-large-bold` | 22px | 32px | 700 | Títulos de tarjetas con énfasis |
| `typography-title-medium` | 16px | 24px | 500 | Títulos de listas, secciones |
| `typography-title-small` | ~~11px~~ **14px** | 20px | 500 | Labels de navegación, tabs |
| `typography-label-large` | 14px | 20px | 500 | Botones, chips |
| `typography-label-medium` | 12px | 16px | ~~400~~ **500** | Labels secundarios |
| `typography-label-medium-semibold` | 12px | 16px | 600 | Labels con énfasis |
| `typography-label-small` | ~~12px~~ **11px** | 16px | ~~400~~ **500** | Labels mínimos, badges |
| `typography-body-large` | 16px | 24px | ~~500~~ **400** | Texto de contenido principal |
| `typography-body-large-bold` | 16px | 24px | 700 | Texto de contenido con énfasis |
| `typography-body-medium` | 14px | 20px | 400 | Texto secundario, descripciones |
| `typography-body-medium-semibold` | 14px | 20px | 600 | Texto secundario con énfasis |
| `typography-body-small` | 12px | 16px | 400 | Texto auxiliar, notas, captions |
| `typography-body-small-semibold` | 12px | 16px | 600 | Captions con énfasis |

> **Corregido en v3.0:**
> - `title-small` tenía 11px, tamaño que en M3 corresponde a `label-small`, no a `title-small` (14px). Corregido a 14px.
> - `label-medium` tenía peso 400; M3 especifica 500. Corregido.
> - `label-small` tenía el mismo valor exacto que `label-medium` y `body-small` (12px/16px/400) — tres tokens indistinguibles entre sí. Corregido a 11px/500 (valor M3 de label-small), para que vuelva a ser un token diferenciado.
>
> **Regla de peso tipográfico (confirmada con el equipo en v3.0):** el uso de negrita en encabezados **es una decisión de marca deliberada**, no un error — MoonDesk quiere una identidad más "bold" en títulos. Se documenta como regla explícita y sistemática, ya no caso por caso:
> - `display-*`, `headline-*` → siempre **700**
> - `title-large` → **400** (usar `title-large-bold` cuando se necesite énfasis puntual)
> - `title-medium` / `title-small` → **500** (como M3)
> - `body-*` → siempre **400** (usar la variante `-bold`/`-semibold` explícita cuando se necesite énfasis)
> - `label-*` → **500** (como M3)
>
> Como parte de esta regla, `body-large` se corrigió de 500 a **400** — era la única excepción que no encajaba en el patrón (ya existe `body-large-bold` para los casos que necesiten énfasis, así que no hacía falta que el valor base fuera medium).

### Principios tipográficos

- Jerarquía clara y consistente entre componentes
- Labels persistentes en formularios — nunca reemplazados por placeholder
- Botones siempre en `typography-label-large`

---

## 5. Espaciado

Sistema base de **8pt grid** — todos los valores son múltiplos de 4px.

| Token | Valor | Uso |
|---|---|---|
| `spacing-xxs` | 4px | Micro spacing, separación interna de iconos |
| `spacing-xs` | 8px | Separaciones mínimas entre elementos |
| `spacing-sm` | 12px | Agrupación compacta |
| `spacing-md` | 16px | Padding estándar, separación base |
| `spacing-lg` | 24px | Separación entre secciones, padding de cards |
| `spacing-xl` | 32px | Layout interno de secciones |
| `spacing-2xl` | 40px | Separaciones amplias |
| `spacing-3xl` | 48px | Bloques de contenido |
| `spacing-4xl` | 64px | Macro layout |

### Grid

- Sistema: 12 columnas
- Gutters: 16px (mobile) / 24px (desktop)
- Max width: 1200px
- Padding de contenido: 16px (mobile) / 24px (desktop)

---

## 6. Border Radius

| Token | Valor | Uso |
|---|---|---|
| `radius-xs` | 4px | Inputs, chips pequeños |
| `radius-sm` | 8px | Botones, badges |
| `radius-md` | 12px | Cards compactas |
| `radius-lg` | 16px | Cards estándar, paneles |
| `radius-xl` | 28px | Diálogos, bottom sheets, FAB |
| `radius-full` | 9999px | Avatares, pills, elementos circulares |

---

## 7. Elevación

La elevación se expresa mediante `box-shadow`. El color base es siempre `color-shadow` (`#181C22`).

| Token | box-shadow | Uso |
|---|---|---|
| `elevation-0` | `none` | Superficie plana |
| `elevation-1` | `0px 1px 2px rgba(24,28,34,0.08)` | Cards básicas |
| `elevation-2` | `0px 2px 4px rgba(24,28,34,0.10)` | Componentes interactivos |
| `elevation-3` | `0px 4px 8px rgba(24,28,34,0.12)` | Cards destacadas, dropdowns |
| `elevation-4` | `0px 8px 16px rgba(24,28,34,0.14)` | Diálogos, modales |
| `elevation-5` | `0px 12px 24px rgba(24,28,34,0.16)` | Elementos flotantes, FAB |

> **⚠️ Gap conocido (v3.0):** la elevación de M3 combina sombra + una capa de "surface tint" (un tinte del color primario que aumenta con la elevación). Este sistema solo modela la sombra. Una vez migrado el theming a la API M3 real (Sección 9), Angular Material aplicará ese tinte automáticamente — validar visualmente si Figma debe incorporarlo para representar fielmente el resultado en producción.

---

## 8. Variables CSS

CSS custom properties listas para usar. Se declaran en `:root` del proyecto.

```css
:root {

  /* ── COLOR: Primarios ── */
  --color-primary:                    #00497E;
  --color-on-primary:                 #FFFFFF;
  --color-primary-container:          #EAF1FF;
  --color-on-primary-container:       #0061A6;
  --color-inverse-primary:            #A0CAFF;

  /* ── COLOR: Secundarios ── */
  --color-secondary:                  #4E6078;
  --color-on-secondary:               #FFFFFF;
  --color-secondary-container:        #CCDDF7;
  --color-on-secondary-container:     #36485F;

  /* ── COLOR: Terciarios ── */
  --color-tertiary:                   #00838F;
  --color-on-tertiary:                #FFFFFF;
  --color-tertiary-container:         #E0F7FA;
  --color-on-tertiary-container:      #006972;   /* corregido en v3.0 (antes #00ACC1) */

  /* ── COLOR: Error ── */
  --color-error:                      #A83445;
  --color-on-error:                   #FFFFFF;
  --color-error-container:            #FFECEC;
  --color-on-error-container:         #A83445;   /* corregido en v3.0 (antes #C94C5C) */

  /* ── COLOR: Superficies ── */
  --color-surface-dim:                #D8DBE1;
  --color-surface:                    #F7F8FF;
  --color-surface-bright:             #FFFFFF;
  --color-surface-container-lowest:   #FFFFFF;   /* corregido en v3.0 (antes #F2F5FA) */
  --color-surface-container-low:      #EDEEF7;
  --color-surface-container:          #E6E7F0;
  --color-surface-container-high:     #DDE0EA;
  --color-surface-container-highest:  #D5D8E3;

  /* ── COLOR: Texto y contorno ── */
  --color-on-surface:                 #181C22;
  --color-on-surface-variant:         #5A5F66;
  --color-outline:                    #73777F;
  --color-outline-variant:            #A8ABB3;

  /* ── COLOR: Inversas y especiales ── */
  --color-inverse-surface:            #2D3038;
  --color-inverse-on-surface:         #EFF0F7;
  --color-scrim:                      rgba(24, 28, 34, 0.32);
  --color-shadow:                     #181C22;

  /* ── COLOR: Estados ── */
  --color-warning:                    #D58B32;
  --color-warning-container:          #FDF5EB;
  --color-on-warning-container:       #935E1E;   /* corregido en v3.0 (antes #EEAD5F, fallaba contraste) */
  --color-success:                    #43A047;
  --color-success-container:          #E8F5E9;
  --color-on-success-container:       #317534;   /* corregido en v3.0 (antes #66BB6A, fallaba contraste) */

  /* ── TIPOGRAFÍA ── */
  --font-family-base:                 'Inter', sans-serif;
  --typography-display-small:         700 36px/44px var(--font-family-base);
  --typography-headline-large:        700 32px/40px var(--font-family-base);
  --typography-headline-medium:       700 28px/36px var(--font-family-base);
  --typography-headline-small:        700 24px/32px var(--font-family-base);
  --typography-title-large:           400 22px/32px var(--font-family-base);
  --typography-title-large-bold:      700 22px/32px var(--font-family-base);
  --typography-title-medium:          500 16px/24px var(--font-family-base);
  --typography-title-small:           500 14px/20px var(--font-family-base);  /* corregido: tamaño 14px (antes 11px) */
  --typography-label-large:           500 14px/20px var(--font-family-base);
  --typography-label-medium:          500 12px/16px var(--font-family-base); /* corregido: peso 500 (antes 400) */
  --typography-label-medium-sb:       600 12px/16px var(--font-family-base);
  --typography-label-small:           500 11px/16px var(--font-family-base); /* corregido: 11px/500 (antes 12px/400, idéntico a label-medium) */
  --typography-body-large:            400 16px/24px var(--font-family-base); /* corregido: peso 400 (antes 500), ver regla de pesos en Sección 4 */
  --typography-body-large-bold:       700 16px/24px var(--font-family-base);
  --typography-body-medium:           400 14px/20px var(--font-family-base);
  --typography-body-medium-sb:        600 14px/20px var(--font-family-base);
  --typography-body-small:            400 12px/16px var(--font-family-base);
  --typography-body-small-sb:         600 12px/16px var(--font-family-base);

  /* ── ESPACIADO ── */
  --spacing-xxs:   4px;
  --spacing-xs:    8px;
  --spacing-sm:    12px;
  --spacing-md:    16px;
  --spacing-lg:    24px;
  --spacing-xl:    32px;
  --spacing-2xl:   40px;
  --spacing-3xl:   48px;
  --spacing-4xl:   64px;

  /* ── BORDER RADIUS ── */
  --radius-xs:     4px;
  --radius-sm:     8px;
  --radius-md:     12px;
  --radius-lg:     16px;
  --radius-xl:     28px;
  --radius-full:   9999px;

  /* ── ELEVACIÓN ── */
  --elevation-0:   none;
  --elevation-1:   0px 1px 2px rgba(24, 28, 34, 0.08);
  --elevation-2:   0px 2px 4px rgba(24, 28, 34, 0.10);
  --elevation-3:   0px 4px 8px rgba(24, 28, 34, 0.12);
  --elevation-4:   0px 8px 16px rgba(24, 28, 34, 0.14);
  --elevation-5:   0px 12px 24px rgba(24, 28, 34, 0.16);
}
```

---

## 9. Integración Angular Material

> **Reescrito en v3.0.** La versión anterior usaba la API legacy de Angular Material (M2): `mat.define-palette`, `mat.define-light-theme`, `mat.define-typography-config` con niveles `$headline-1`/`$subtitle-1`. Angular Material 19 usa M3 por defecto, y esa API antigua deja el sistema de tokens de este documento desconectado de lo que realmente se renderiza. Abajo, el enfoque correcto para v19.

### Tema MoonDesk (API M3)

```scss
// _moondesk-theme.scss
@use '@angular/material' as mat;

// 1. Definir el tema M3 a partir de los primitivos de MoonDesk.
//    mat.define-theme acepta paletas M3 (tonal palettes); si Angular Material
//    no trae una paleta que coincida con la de MoonDesk, generarla con la
//    herramienta de temas de Material (Material Theme Builder) a partir de
//    los mismos primitivos de la Sección 2, para que el "40" de Angular
//    Material sea el mismo "40" que ya está documentado aquí.
$moondesk-theme: mat.define-theme((
  color: (
    theme-type: light,
    primary: mat.$blue-palette,     // reemplazar por la paleta tonal propia de MoonDesk
    tertiary: mat.$cyan-palette,    // reemplazar por la paleta tonal propia de MoonDesk
  ),
  typography: (
    brand-family: 'Inter',
    // Los niveles de tipografía M3 (display/headline/title/body/label,
    // cada uno large/medium/small) deben configurarse aquí con los MISMOS
    // valores de la Sección 4, no con los niveles M2 ($headline-1, etc.)
  ),
  density: (
    scale: 0,
  ),
));

html {
  @include mat.theme($moondesk-theme);
}
```

### Mapeo de tokens (obligatorio)

Angular Material 19 genera automáticamente variables de sistema `--mat-sys-*` (`--mat-sys-primary`, `--mat-sys-on-primary`, `--mat-sys-surface-container`, etc.). Estas **deben coincidir** con los tokens `--color-*` de la Sección 8. Dos formas de lograrlo:

1. **Generar el tema M3 desde los primitivos de MoonDesk** (recomendado) — así `--mat-sys-*` y `--color-*` son, por construcción, los mismos valores.
2. **Mapear manualmente** cada `--color-*` a su `--mat-sys-*` equivalente si por alguna razón no se puede regenerar el tema (mantenimiento más frágil, evitar si es posible).

Sin uno de estos dos pasos, los componentes de Angular Material seguirán renderizando con su paleta M3 por defecto, no con la de MoonDesk.

### Naming conventions

| Elemento | Convención | Ejemplo |
|---|---|---|
| Tokens CSS | kebab-case | `--color-primary` |
| Componentes Angular | PascalCase | `MoonDeskButtonComponent` |
| Props / Inputs | camelCase | `isDisabled`, `variantType` |
| Archivos SCSS | kebab-case | `_moondesk-theme.scss` |

---

## 10. Componentes — Diálogos

*(Sin cambios respecto a v2.0 — este componente ya está correctamente especificado y sirve de plantilla para el resto).*

### Descripción

Los diálogos proveen prompts importantes dentro de un flujo de usuario. Pueden requerir una acción, comunicar información para tomar decisiones o ayudar al usuario a completar una tarea específica.

### Component tokens

| Token | Token semántico | Valor |
|---|---|---|
| `dialog-background` | `color-surface-container-high` | `#DDE0EA` |
| `dialog-border-radius` | `radius-xl` | `28px` |
| `dialog-elevation` | `elevation-4` | `0px 8px 16px rgba(24,28,34,0.14)` |
| `dialog-scrim` | `color-scrim` | `rgba(24,28,34,0.32)` |
| `dialog-title-color` | `color-on-surface` | `#181C22` |
| `dialog-title-typography` | `typography-headline-small` | `700 24px/32px` |
| `dialog-body-color` | `color-on-surface-variant` | `#5A5F66` |
| `dialog-body-typography` | `typography-body-medium` | `400 14px/20px` |
| `dialog-padding-x` | `spacing-lg` | `24px` |
| `dialog-padding-y` | `spacing-lg` | `24px` |
| `dialog-min-width` | — | `280px` |
| `dialog-max-width` | — | `560px` |
| `dialog-action-gap` | `spacing-xs` | `8px` |
| `dialog-icon-color` | `color-secondary` | `#4E6078` |
| `dialog-divider-color` | `color-outline-variant` | `#A8ABB3` |

### Variantes

| Variante | Descripción | Botones |
|---|---|---|
| Basic | Título + cuerpo de texto | Text (2 acciones) |
| Basic con icono | Icono centrado + título + cuerpo | Filled (2 acciones) |
| Con lista | Título + cuerpo + lista de checkboxes | Text (2 acciones) |
| Con lista e icono | Icono centrado + título + lista | Text (2 acciones) |

### Jerarquía de botones en diálogos

- **Acción afirmativa** → botón a la derecha, siempre
- **Acción de cancelación** → botón a la izquierda, siempre
- Diálogos con icono → botones `Filled`
- Diálogos sin icono → botones `Text`
- Solo una acción primaria por diálogo

### Estados requeridos

| Estado | Descripción |
|---|---|
| Default | Estado inicial del diálogo al abrirse |
| Loading | Spinner interno mientras se procesa la acción |
| Error | Mensaje de error inline, sin cerrar el diálogo |
| Con scroll | Cuando el contenido supera el alto máximo disponible |
| Destructivo | Variante para acciones irreversibles (eliminar, sobrescribir) |

### Comportamiento

- Se abre con animación de escala desde el centro (`ease-out`, 200ms)
- El scrim bloquea la interacción con el contenido de fondo
- Cerrar con clic en scrim está habilitado por defecto, salvo en acciones críticas
- En mobile: evaluar bottom sheet en lugar de dialog centrado
- Soporte completo de teclado: `Tab` para navegar acciones, `Esc` para cerrar

### Accesibilidad

- `role="dialog"` en el contenedor
- `aria-labelledby` apuntando al título
- `aria-describedby` apuntando al cuerpo
- Focus inicial en el primer elemento interactivo al abrir
- Focus trap mientras el diálogo está abierto

---

## 11. Componentes pendientes de documentar

Nuevo en v3.0. Estos componentes ya existen visualmente en Figma (carpeta "DS Foundations & Components") pero **no tienen todavía** una especificación escrita equivalente a la de Diálogos (Sección 10): tokens propios, lista de estados, comportamiento y accesibilidad. Se documentan aquí en el orden de prioridad acordado (los de mayor uso en una app B2B interna primero):

1. Buttons (filled, outline, text, icon button)
2. Text field / Search field
3. Checkbox
4. Radio button
5. Switch
6. Tabla de datos / Paginator
7. Diálogo *(✅ ya documentado, Sección 10)*
8. Navegación (Toolbar / barra superior)
9. Cards
10. Chips
11. Menús
12. Tooltips
13. Snackbar
14. Badges
15. Tabs

Antes de dar un componente por "consolidado" en Figma, cada uno de ellos necesita, como mínimo: sus tokens propios, sus estados completos **con etiqueta visible indicando cuál es cuál** (Default/Hover/Focus/Active/Disabled/Loading/Error, según aplique), su resolución de accesibilidad, y su comportamiento responsive.

---

## 12. Guidelines UX

### Jerarquía de acciones

| Nivel | Tipo de botón | Uso |
|---|---|---|
| Alto | Filled | Acción primaria, un solo CTA por vista |
| Medio | Tonal | Acción destacada secundaria |
| Bajo | Outlined | Alternativa o acción complementaria |
| Mínimo | Text | Acción de baja prioridad |

> Solo una acción primaria por vista. Evitar competencia visual entre CTAs.

### Feedback de interacción

| Evento | Tiempo máximo |
|---|---|
| Feedback visual (hover, focus, active) | ≤ 100ms |
| Respuesta de sistema (loading, error, success) | ≤ 300ms |

### Estados obligatorios por componente interactivo

Todo componente interactivo debe implementar los siguientes estados:

- Default
- Hover
- Focus (siempre visible)
- Active
- Disabled
- Loading
- Error
- Success

### Accesibilidad

- Contraste de texto normal: mínimo **4.5:1**
- Contraste de texto grande: mínimo **3:1**
- Navegación completa por teclado
- Focus visible y consistente en todos los componentes
- Cumplimiento WCAG 2.1 AA

### Motion

- Duración: 150–300ms
- Entrada: `ease-out`
- Salida: `ease-in`

> Toda animación debe comunicar estado o cambio, nunca es decorativa.

### Content design

- Botones → verbo + resultado: *"Guardar cambios"*, *"Eliminar archivo"*
- Mensajes de error → causa + solución: *"No se pudo guardar. Revisá tu conexión e intentá de nuevo."*
- Labels → descriptivos y sin ambigüedad

### Densidad de interfaz

| Modo | Uso |
|---|---|
| Comfortable | Flujos generales, onboarding |
| Compact | Dashboards, tablas de datos |
| Dense | Herramientas expertas, vistas de gestión masiva |

---

## 13. Gobernanza

### Reglas de uso

- No modificar tokens primitivos sin validación del equipo de diseño
- No hardcodear valores de color, tipografía o espaciado fuera del sistema de tokens
- Priorizar componentes existentes antes de crear nuevos
- Toda nueva variante requiere documentación en Figma y en este sistema
- **Nuevo en v3.0:** ningún componente nuevo se marca como "consolidado" sin: sus estados etiquetados explícitamente, su verificación de contraste (con el mismo método usado en el Changelog), y su mapeo confirmado contra el theming real de Angular Material.

### Propuesta de cambio

1. Identificar el gap o mejora
2. Proponer en el canal de diseño con mockup o especificación
3. Revisión por parte del equipo de diseño
4. Aprobación y actualización en Figma (fuente de verdad)
5. Actualización en el sistema de tokens y documentación
6. Comunicación al equipo de desarrollo

### Evolución del sistema

MoonDesk Design System es un sistema vivo. Itera con feedback real del producto, escala con nuevos componentes y mantiene alineación constante entre diseño y desarrollo.

---

## Changelog v2.0 → v3.0

Basado en la auditoría de septiembre 2026 contra Angular Material 19 (M3) y WCAG 2.1 AA.

| # | Cambio | Tipo |
|---|---|---|
| 1 | Reescrita la Sección 9 (Integración Angular Material): de API M2 legacy a `mat.define-theme` / M3, con mapeo obligatorio a `--mat-sys-*` | Corrección crítica |
| 2 | `neutral-100` corregido de `#F2F5FA` a `#FFFFFF` (rompía la escala tonal) | Corrección crítica |
| 3 | `color-on-tertiary-container` corregido de tertiary-50 a tertiary-30 (contraste 2.46:1 → 5.78:1) | Corrección crítica |
| 4 | `color-on-error-container` corregido de error-50 a error-40 (contraste 3.95:1 → 5.69:1) | Corrección crítica |
| 5 | `color-on-warning-container` y `color-on-success-container` corregidos: se agregaron los primitivos `orange-30` (#935E1E) y `green-30` (#317534) — ningún tono existente alcanzaba 4.5:1. Verificado: 5.03:1 y 5.01:1 | Corrección crítica (cerrado en revisión conjunta) |
| 6 | `typography-title-small` corregido de 11px a 14px | Corrección |
| 7 | `typography-label-medium` corregido de peso 400 a 500 | Corrección |
| 8 | `typography-label-small` corregido de 12px/400 (idéntico a label-medium y body-small) a 11px/500 | Corrección |
| 9 | Confirmado con el equipo: el peso bold en headlines/display **es intencional** (identidad de marca). Se documentó como regla explícita y sistemática (display/headline siempre 700, title-medium/small y labels 500, body siempre 400). Como parte de la regla, `body-large` se corrigió de 500 a 400 (era la única excepción sin patrón) | Cerrado — decisión de equipo |
| 10 | Señalado el gap de "surface tint" en el modelo de elevación (solo box-shadow, sin tinte M3) | A resolver tras migrar Sección 9 |
| 11 | Agregada la Sección 11 (Componentes pendientes de documentar), con la lista priorizada de los ~15 componentes que existen en Figma pero no tienen spec escrita | Nuevo |
| 12 | Agregada regla de gobernanza: ningún componente se marca "consolidado" sin estados etiquetados, contraste verificado y mapeo a Angular Material confirmado | Nuevo |

**Hallazgos de la auditoría que aún no están reflejados en este documento** (viven en la librería de Figma, no en los tokens, y se resuelven ahí): falta de etiquetas de estado en toda la librería; variante roja de "error" en Checkbox con semántica sin confirmar; Text field genérico mostrando siempre ícono de búsqueda; botones con ícono a ambos lados como ejemplo por defecto; duplicación "Toolbar" / "Barra de Navegación"; Snackbar sin variantes por severidad; parte de "Tabs" correspondiendo en realidad a un patrón de bottom navigation no nativo de Angular Material. Ver el documento **MoonDesk_DS_Auditoria_y_Ajustes.docx** para el detalle completo de estos puntos.

---

*MoonDesk Design System v3.0 — Septiembre 2026*
*Fuente de verdad visual: Figma — DS Foundations & Components*
