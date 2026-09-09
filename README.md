# Guía de Open Graph y Twitter Card

Cheatsheet práctico con los meta tags exactos para controlar cómo se ve un enlace al compartirlo en redes sociales. Pensado para desarrolladores y profesionales SEO que necesitan el snippet correcto sin bucear en la documentación de cada plataforma.

Este README cubre los tags por bloque. Si prefieres verlos todos juntos en un `<head>` real, mira [ejemplo/articulo-blog-completo.html](ejemplo/articulo-blog-completo.html).

> Mantenido por [SapyensDev](https://sapyensdev.com), desarrollo web y SEO técnico.

## Índice

- [Cómo usar esta guía](#cómo-usar-esta-guía)
- [Los tags base de Open Graph](#los-tags-base-de-open-graph)
- [Open Graph por tipo de página](#open-graph-por-tipo-de-página)
- [Twitter Card](#twitter-card)
- [Pinterest Rich Pins](#pinterest-rich-pins)
- [Apple Smart App Banner](#apple-smart-app-banner)
- [Requisitos de imagen por plataforma](#requisitos-de-imagen-por-plataforma)
- [Errores comunes](#errores-comunes)
- [Cómo validar](#cómo-validar)

## Cómo usar esta guía

Cada sección incluye:
1. **Qué tags necesitas** y cuáles son obligatorios
2. **Snippet HTML mínimo funcional**, listo para copiar en el `<head>`
3. **Errores frecuentes** de esa plataforma o tipo de página

Copia el snippet, sustituye los valores por los reales de tu página y valida siempre antes de publicar.

### Aviso importante antes de empezar

Los scrapers de Facebook, Twitter/X, LinkedIn y WhatsApp no ejecutan JavaScript. Si los meta tags se inyectan por JS (típico en aplicaciones React/Vue sin renderizado del lado del servidor), el scraper ve la página vacía o con los valores por defecto del `<head>` inicial, no los que genera el cliente. Los tags tienen que estar en el HTML que devuelve el servidor, ya sea con SSR, SSG, o generados directamente por el CMS. Esta es la causa más frecuente de un Open Graph "bien puesto" que no funciona en producción.

---

## Los tags base de Open Graph

Open Graph es el protocolo que crearon Facebook en 2010 y que hoy leen prácticamente todas las redes y apps de mensajería (WhatsApp, Telegram, Slack, Discord, LinkedIn, Pinterest). Va en el `<head>` de la página, no en el `<body>`.

**Obligatorios:** `og:title`, `og:type`, `og:image`, `og:url`
**Recomendados:** `og:description`, `og:site_name`, `og:locale`

```html
<meta property="og:title" content="Título de la página, no el H1 exacto si el H1 es muy largo" />
<meta property="og:description" content="Descripción corta, 2-3 frases máximo" />
<meta property="og:image" content="https://ejemplo.com/imagen-og.jpg" />
<meta property="og:image:width" content="1200" />
<meta property="og:image:height" content="630" />
<meta property="og:image:alt" content="Descripción de la imagen para lectores de pantalla" />
<meta property="og:url" content="https://ejemplo.com/pagina" />
<meta property="og:type" content="website" />
<meta property="og:site_name" content="Nombre de la empresa" />
<meta property="og:locale" content="es_ES" />
```

**Múltiples imágenes de fallback:** puedes repetir `og:image` con varias opciones, la plataforma elige la que mejor encaje con su formato. Útil si tienes una versión horizontal y otra cuadrada de la misma imagen.

```html
<meta property="og:image" content="https://ejemplo.com/imagen-horizontal.jpg" />
<meta property="og:image" content="https://ejemplo.com/imagen-cuadrada.jpg" />
```

**Errores frecuentes:**
- `og:url` con parámetros UTM o trailing slash inconsistente, lo que genera cachés distintas del mismo enlace en Facebook
- `og:image` con ruta relativa en vez de URL absoluta. Muchas plataformas simplemente no la cargan
- Omitir `og:type`, con lo que Facebook asume `website` aunque la página sea un artículo o producto
- Omitir `og:image:width`/`og:image:height`, lo que puede retrasar o romper el renderizado del preview mientras la plataforma calcula el tamaño por su cuenta

---

## Open Graph por tipo de página

### Artículo de blog

```html
<meta property="og:type" content="article" />
<meta property="article:published_time" content="2026-09-01T08:00:00+02:00" />
<meta property="article:modified_time" content="2026-09-09T10:00:00+02:00" />
<meta property="article:author" content="https://ejemplo.com/autor/nombre" />
<meta property="article:section" content="SEO" />
<meta property="article:tag" content="core web vitals" />
```

### Producto (e-commerce)

```html
<meta property="og:type" content="product" />
<meta property="product:price:amount" content="29.99" />
<meta property="product:price:currency" content="EUR" />
<meta property="product:availability" content="in stock" />
<meta property="product:retailer_item_id" content="SKU-001" />
```

### Perfil de persona

```html
<meta property="og:type" content="profile" />
<meta property="profile:first_name" content="Nombre" />
<meta property="profile:last_name" content="Apellido" />
<meta property="profile:username" content="nombreusuario" />
```

**Errores frecuentes:**
- Usar `article` en páginas de servicio o producto, lo que confunde a Facebook sobre qué tags adicionales esperar
- `product:price:amount` desincronizado del precio real mostrado en la página

---

## Twitter Card

Twitter (hoy X) no adoptó Open Graph y creó su propio protocolo, con sus propios nombres de propiedad. Hace fallback a algunos campos `og:*` si faltan los `twitter:*` equivalentes, pero conviene poner ambos bloques completos para asegurar la tarjeta grande (`summary_large_image`), la más deseable visualmente.

**Obligatorio:** `twitter:card`
**Recomendados:** `twitter:title`, `twitter:description`, `twitter:image`, `twitter:site`

```html
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:site" content="@sapyensdev" />
<meta name="twitter:title" content="Título de la página" />
<meta name="twitter:description" content="Descripción corta, 2-3 frases máximo" />
<meta name="twitter:image" content="https://ejemplo.com/imagen-twitter.jpg" />
<meta name="twitter:image:alt" content="Descripción de la imagen para lectores de pantalla" />
```

**Tipos de `twitter:card`:**
- `summary`: tarjeta pequeña, imagen cuadrada a la izquierda
- `summary_large_image`: tarjeta grande, imagen a todo el ancho arriba (la que casi siempre conviene usar)
- `app`: enlace directo a una app móvil
- `player`: contenido embebible (vídeo, audio)

**Errores frecuentes:**
- Omitir `twitter:card` por completo. Sin él, X ignora el resto de tags `twitter:*` y solo hace fallback parcial a Open Graph
- Usar `summary` cuando la imagen es horizontal y de buena calidad, desaprovechando el formato grande

---

## Pinterest Rich Pins

Pinterest no tiene protocolo propio. Lee los tags Open Graph que ya tienes y añade campos extra según el tipo de Rich Pin. Requiere aplicar la validación en el [Rich Pins Validator](https://developers.pinterest.com/tools/url-debugger/) para activarse, poner los tags no basta por sí solo.

```html
<meta property="og:type" content="product" />
<meta property="og:title" content="Nombre del producto" />
<meta property="og:image" content="https://ejemplo.com/producto.jpg" />
<meta property="product:price:amount" content="29.99" />
<meta property="product:price:currency" content="EUR" />
<meta property="og:availability" content="instock" />
```

**Errores frecuentes:**
- Poner los tags pero no pasar la validación oficial, con lo que el Rich Pin nunca se activa aunque el markup sea correcto
- `og:availability` con un valor que no está en la lista aceptada de Pinterest (`instock`, `oos`, `pending`, `discontinued`)

---

## Apple Smart App Banner

No es un protocolo de tarjeta social, es un banner nativo de iOS que invita a instalar tu app cuando alguien visita la web desde Safari en iPhone/iPad. Va aparte de todo lo anterior.

```html
<meta name="apple-itunes-app" content="app-id=123456789, app-argument=https://ejemplo.com/pagina" />
```

**Errores frecuentes:**
- Confundirlo con Open Graph y esperar que afecte a cómo se ve el enlace al compartirlo. Solo afecta a Safari en iOS, no a redes sociales
- `app-id` incorrecto, lo que hace que el banner enlace a otra app distinta en la App Store

---

## Requisitos de imagen por plataforma

| Plataforma | Tamaño recomendado | Ratio | Peso máximo |
|---|---|---|---|
| Open Graph (general) | 1200x630px | 1.91:1 | 8 MB |
| Twitter `summary_large_image` | 1200x628px | 1.91:1 | 5 MB |
| Twitter `summary` | 144x144px mínimo | 1:1 | 5 MB |
| Pinterest Rich Pin | 1000x1500px | 2:3 | sin límite documentado |

**Errores frecuentes:**
- Usar una imagen cuadrada para `summary_large_image`, lo que Twitter recorta de forma poco favorecedora
- Imágenes por debajo de 200x200px, que Facebook rechaza directamente para el rich preview

---

## Errores comunes

Independientemente de la plataforma:

- **Caché desactualizada.** Facebook, LinkedIn y Twitter cachean el preview la primera vez que alguien comparte el enlace. Si cambias los tags después, hay que forzar el re-scrape con la herramienta de validación de cada plataforma, no basta con esperar
- **Texto duplicado entre `og:description` y `meta name="description"`**, lo que no es un error técnico pero desaprovecha la oportunidad de escribir cada uno para su audiencia (SERP vs. red social)
- **Imagen servida solo en WebP sin fallback.** Algunas plataformas de mensajería todavía no renderizan bien WebP en el preview, mejor usar JPG o PNG para las imágenes OG específicamente

## Cómo validar

1. **[Facebook Sharing Debugger](https://developers.facebook.com/tools/debug/):** fuerza el re-scrape y muestra qué tags Open Graph detecta
2. **[Twitter Card Validator](https://cards-dev.twitter.com/validator):** herramienta oficial, hoy con acceso limitado, alternativa práctica es compartir el enlace en un DM propio para ver la preview
3. **[LinkedIn Post Inspector](https://www.linkedin.com/post-inspector/):** muestra el preview exacto y fuerza actualización de caché
4. **[Pinterest Rich Pins Validator](https://developers.pinterest.com/tools/url-debugger/):** valida y activa Rich Pins

---

## Licencia

MIT. Usa, copia y adapta libremente.

## Contribuir

Pull requests bienvenidas, correcciones, plataformas adicionales, ejemplos nuevos. Abre un issue si tienes dudas sobre algún caso concreto.
