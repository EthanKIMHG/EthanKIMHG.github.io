---
layout: post
title:  "Fonts in Web Development"
date:   2025-08-03 00:00:00 +0900
categories: jekyll update
---
# Fonts in Web Development

Fonts are a critical aspect of web design, significantly impacting readability, aesthetics, and user experience. Understanding how to use and manage fonts effectively is essential for frontend developers.

## Types of Fonts for the Web

1.  **Web Fonts (Custom Fonts):**
    *   These are font files (e.g., WOFF, WOFF2, TTF, OTF, EOT) that are downloaded by the browser when a user visits a webpage. They allow designers to use virtually any typeface, providing greater creative freedom and brand consistency.
    *   **Sources:** Google Fonts, Adobe Fonts, Font Squirrel, self-hosting.
    *   **Formats:**
        *   **WOFF (Web Open Font Format):** The most widely supported and recommended format for web fonts due to its good compression and broad browser support.
        *   **WOFF2:** An improved version of WOFF with even better compression, leading to smaller file sizes and faster loading. Recommended for modern browsers.
        *   **TTF (TrueType Font) / OTF (OpenType Font):** Older formats, less optimized for web use but still supported by some browsers. Larger file sizes.
        *   **EOT (Embedded OpenType):** Microsoft's proprietary format, primarily for older Internet Explorer versions.

2.  **System Fonts (Web Safe Fonts):**
    *   These are fonts that are pre-installed on the user's operating system (e.g., Arial, Helvetica, Times New Roman, Georgia, Verdana). Using them ensures that the text will display consistently across different systems without requiring font downloads.
    *   **Pros:** Fast loading (no download needed), reliable display.
    *   **Cons:** Limited design flexibility, generic appearance.

## How to Use Web Fonts (`@font-face`)

Web fonts are typically embedded using the `@font-face` CSS rule. This rule allows you to define custom fonts that can be loaded from a remote server or a local file.

```css
@font-face {
  font-family: 'MyCustomFont'; /* A name you choose for your font */
  src: url('/fonts/MyCustomFont.woff2') format('woff2'),
       url('/fonts/MyCustomFont.woff') format('woff');
  font-weight: normal;
  font-style: normal;
  font-display: swap; /* How the font is displayed while loading */
}

body {
  font-family: 'MyCustomFont', Arial, sans-serif; /* Use your custom font, with fallbacks */
}
```

**Key properties within `@font-face`:**
*   `font-family`: The name you assign to the font.
*   `src`: Specifies the path to the font files. It's crucial to provide multiple formats for cross-browser compatibility, with `woff2` first for modern browsers.
*   `format()`: Specifies the format of the font file, helping the browser choose the correct one.
*   `font-weight`, `font-style`: Define the weight and style of the font (e.g., `bold`, `italic`). You should define separate `@font-face` rules for each weight/style variation.
*   `font-display`: Controls how a font face is displayed based on whether it has downloaded and is ready to use. Common values:
    *   `auto`: Browser default.
    *   `block`: Gives the font face a short block period and an infinite swap period. Text is invisible until the font loads.
    *   `swap`: Gives the font face an extremely small block period and an infinite swap period. Text is rendered immediately with a fallback font, and swapped when the custom font loads.
    *   `fallback`: Gives the font face an extremely small block period and a short swap period. Text is rendered with a fallback, and swapped if the custom font loads within the swap period. Otherwise, the fallback is used permanently.
    *   `optional`: Gives the font face an extremely small block period and no swap period. Text is rendered with a fallback, and the custom font is only used if it loads quickly.

## Font Loading Strategies and Performance

Web fonts can impact page load performance. Strategies to mitigate this:

1.  **`font-display: swap`:** (Most common and recommended) Provides a good balance between performance and design. Users see text immediately with a fallback, then the custom font swaps in.
2.  **Preloading Fonts:** Use `<link rel="preload" as="font" type="font/woff2" crossorigin>` in your HTML `<head>` to tell the browser to fetch critical fonts early.
    ```html
    <link rel="preload" href="/fonts/MyCustomFont.woff2" as="font" type="font/woff2" crossorigin>
    ```
3.  **Font Subsetting:** Remove unused glyphs (characters) from font files to reduce their size. Tools like Font Squirrel's Webfont Generator can do this.
4.  **Variable Fonts:** Use variable fonts (if applicable) to reduce the number of font files needed for different weights and styles.
5.  **Critical CSS/Font Loading:** Load only the fonts needed for the initial viewport first, and defer others.
6.  **CDN Usage:** Use font services like Google Fonts, which serve fonts from a CDN, often leading to faster delivery.

## Font Fallback Stacks

Always provide a font fallback stack in your `font-family` declaration. This ensures that if your custom web font fails to load, the browser will use a system font that is similar in appearance.

```css
body {
  font-family: 'MyCustomFont', 'Helvetica Neue', Arial, sans-serif;
}
```

*   `'MyCustomFont'`: Your primary web font.
*   `'Helvetica Neue'`: A similar system font (macOS).
*   `Arial`: A common system font (Windows).
*   `sans-serif`: A generic font family that tells the browser to pick any sans-serif font available on the system.

## Icon Fonts vs. SVG Icons

*   **Icon Fonts (e.g., Font Awesome, Material Icons):** Used to be popular for scalable vector icons. However, they have accessibility issues (screen readers might read them as characters) and can suffer from FOUT/FOIT (Flash of Unstyled/Invisible Text).
*   **SVG Icons:** Generally preferred now. They are scalable, accessible, and can be styled with CSS. They don't suffer from font loading issues.

Proper font management is crucial for creating visually appealing and performant web experiences.