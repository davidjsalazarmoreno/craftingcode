---
title: "Unidades CSS: ¿Cuál usar y cuál evitar"
date: 2023-05-06T14:30:58-05:00
draft: true
description: ""
---


EN CONSTRUCCION, si tienes algun feedback o correcion por favor comenta en el video de youtube o tiktok, o escribeme a este correo.
:globe_with_meridians:

| Nombre de unidad CSS | ¿Cuando Usar? | Video explicativo | 
| --------------------|---------------|------------------|
| px                  | Para cosas que no necesites que escalen con el viewport, como `border-width` o `box-shadow`, **NO USAR** en `font-size` o `width` porque rompera el responsive design | [PX en CSS](https://www.tiktok.com/@craftingcode/video/7227476213734837509). |
| em                  | Escalar contenido del elemento actual en base a su font-size y ¿EM para media queries?. | [EM en CSS](TBD) |
| rem                 | Similar a em, pero relativo a la raíz del documento. | [REM en CSS](https://www.tiktok.com/@craftingcode/video/7227855461100440837) |
| %                   | Para tamaños relativos al elemento padre. | No se recomienda para tamaños absolutos o para elementos que no dependen del padre. |
| vw                  | Para tamaños relativos al ancho de la ventana. | No se recomienda para tamaños absolutos o para elementos que no dependen del ancho de la ventana. |
| vh                  | Para tamaños relativos al alto de la ventana. | No se recomienda para tamaños absolutos o para elementos que no dependen del alto de la ventana. |
| fr                  | Para tamaños relativos al alto de la ventana. | [FR en CSS](https://www.tiktok.com/@craftingcode/video/7227102345446837509). |
| ch                  | Para tamaños relativos al alto de la ventana. | [CH en CSS](https://www.tiktok.com/@craftingcode/video/7226754225395551493). |
| qw/qh                  | Para tamaños relativos al alto de la ventana. | [QW/QH](https://www.tiktok.com/@craftingcode/video/71562735914435412540). |


# PX para borders y sombras


:red_circle: Evita PX para font-size ya que no escalará con tu diseño responsive.

```css
.Component {
  font-size: 20px;
}
```

:white_check_mark: Usa PX en sombras o borders ya que no necesitan escalar:

```css
.Component {
  border-bottom: 1px solid #ccc;
  box-shadow: 0 0 10px rgba(0,0,0,.5);
}
```

:zap: **Ejemplo**: [TBD]()


# REM para font-size


:red_circle: Evita PX para font-size ya que no escalará con tu diseño responsive.

```css
.Component {
  font-size: 1.2em;
}
.Component-heading {
  font-size: 1em;
}
.Component-body {
  font-size: 0.75em;
}
```

:white_check_mark: Usa PX en sombras o borders ya que no necesitan escalar:

```css
.Component {
}
.Component-heading {
  font-size: 1.2em;
}
.Component-body {
  font-size: 0.9em;
}
```

:zap: **Ejemplo**: [PX vs EM vs REM](https://codepen.io/indiesquidge/pen/VKoKwy)

# EM para escalar margin y padding

:red_circle: Evita EM para font-size, las unidades EM son relativas al font-size de su elemento actual.

PERO, si ese elemento actual no tiene font-size establecido la unidad EM verá el font-size del elemento padre.

Y si ese no tiene verá el font-size de elemento abuelo, y así sucesivamente.

Esta es una receta para el desastre, causando una cascada de efectos que dañen como se establece el tamaño del font-size en la web.

```css
.abuelo {
  font-size: 12px;
}

.padre {
  /* Olvido colocar el font-size a 8px */
  border: 1px solid red;
}

.hijo {
  /* Esperamos que el font-size sea de 4px: 
  * 0.500rem * 8px (Font-size de .padre) = 4px
  * 
  * PERO como NO agregamos
  * font-size en .padre mirara el font-size de .abuelo :red_circle:
  *
  * 0.500rem * 12px (Font-size de .abuelo) = 6px
  *
  */
  font-size: 0.500rem;
}
```

:white_check_mark: EM para escalar margin y padding con respecto a su font-size

Ya que cuando usamos la unidad EM en anchura, padding o margin será relativa al font-size del elemento actual.

```css
html {
  font-size:  62.5%; /* 62.5% de 16px */
}

.texto {
  font-size: 0.500rem;
  padding: 1em;
  width: 20em;
  margin: 2em;	
}
```

:zap: **Ejemplo**: [TBD]()

# vw y vh para elementos que abarquen todo el viewport

El viewport es esta área visible del navegador.

```css
.fullscreen {
  /* Establece el ancho del elemento al ancho completo de la ventana gráfica */
  width: 100vw;
  /* Establece la altura del elemento a la altura completa de la ventana gráfica */
  height: 100vh;
  background-image: url('imagen.jpg'); 
  /* Hace que la imagen de fondo cubra todo el elemento */
  background-size: cover; 
}
```

https://codepen.io/davidjsalazarmoreno/full/PoyQqQv

# Usa lh para centrar elementos inline-block

La unidad lh es útil para centrar verticalmente iconos en elementos inline-block.

Video explicativo

```css
span {
  background: hotpink;
  border-radius: 50%;
  display: inline-block;
  vertical-align: top;
  margin-left: 1em;
}

.inline-icon {
  display: inline-block;
  width: 1lh;
  height: 1lh;
}
```

Esta unidad es nueva y es experimental, aún quedan muchos casos por cubrir, buenas prácticas por emerger y NO todos los navegadores la soportan.

Por lo que no, no es recomendable usarla en producción al momento de escribir este articulo, si vienes del futuro esto quizás ha cambiado.


Ejemplo solo disponible en chrome https://jsbin.com/rigomah/edit?html,css,output

video explicativo


# Usa ch para establecer max-width

Establecer max-width en un párrafo.

Así nos aseguramos que la anchura máxima de un elemento no sea mayor a 75 caracteres en pantalla, lo que mejorará la legibilidad del texto en tu web.

```css
p {
  max-width: 75ch;
}
``

video explicativo
ejemplo
investigacion de caracteres

# Usa % para establecer max-width



# Fuentes

## ¿Media query con EM o con PX?
- [7 habits of highly effective media queries](https://bradfrost.com/blog/post/7-habits-of-highly-effective-media-queries/)
- [PX, EM or REM Media Queries?](https://zellwk.com/blog/media-query-units/)
- [Don't Use Em for Media Queries](https://adamwathan.me/dont-use-em-for-media-queries/)
- [The EMs have it: Proportional Media Queries FTW!](https://cloudfour.com/thinks/the-ems-have-it-proportional-media-queries-ftw/)
- [Using rem units in media queries and as width](https://stackoverflow.com/questions/47409585/using-rem-units-in-media-queries-and-as-width)
- [To em or not to em? That is the media query question.](https://medium.com/zoosk-engineering/to-em-or-not-to-em-that-is-the-media-query-question-22f4a65e9747)
- [PX, EM, or REM? Examining Media Query Units in 2021.](https://betterprogramming.pub/px-em-or-rem-examining-media-query-units-in-2021-e00cf37b91a9)
- [Switching to Em-Based Media Queries](https://stackoverflow.com/questions/22228568/switching-to-em-based-media-queries9)
- [Making sense of units in CSS Media Queries (in the year 2019)](https://danburzo.ro/media-query-units/)
- [Switch from px to rem for responsive variants? (Tailwind)](https://github.com/tailwindlabs/tailwindcss/discussions/8378)
- [Media queries](https://web.dev/learn/design/media-queries/)
- [Why the font size won't change with browser zoom in?](https://stackoverflow.com/questions/7907760/why-the-font-size-wont-change-with-browser-zoom-in)
- [Media Queries for Different zoom Levels of Browser](https://stackoverflow.com/questions/22223866/media-queries-for-different-zoom-levels-of-browser)
- [Looking At How Browser Zoom Affects CSS Media Queries And Pixel-Density](https://www.bennadel.com/blog/3811-looking-at-how-browser-zoom-affects-css-media-queries-and-pixel-density.htm)
- :zap: [Ejemplo Media query EM vs PX](https://codepen.io/nwalton3/full/MWwYMg)


## ¿REM media queries?
- [REM based media queries are weird](https://smth.uk/rem-based-media-queries-are-weird/)

## ¿Necesitas media queries?
- [The Pros And Cons Of Media Queries Vs Srcset For Responsive Design](https://stackoverflow.com/questions/7907760/why-the-font-size-wont-change-with-browser-zoom-in)

## Unidades PX, REM, y EM
- [Let's talk about units](https://csshell.dev/posts/lets-talk-about-units/)
- [A Cheat Sheet for CSS unit](https://dev.to/pffigueiredo/a-cheat-sheet-for-size-units-4cbi)
- [A Look Into CSS Units: Pixels, EM, and Percentage](https://www.hongkiat.com/blog/css-units/)

## Unidad LH
- [Try out the CSS lh unit in Safari Preview](https://web.archive.org/web/20210625060529/https://webplatform.news/issues/2020-04-29)
- [`lh` and `rlh` units](https://css-tricks.com/lh-and-rlh-units/)
- [Intro to lh and rlh in CSS](https://medium.com/@KasraKhosravi/intro-to-lh-and-rlh-in-css-39414a55d61a)
- [The new (and old) CSS units you've never heard about](https://dev.to/maxart2501/the-new-and-old-css-units-youve-never-heard-about-1mn1)
- :zap: [Ejemplo lh](https://jsbin.com/rigomah/edit?html,css,output)
- :globe_with_meridians: [Soporte lh](https://caniuse.com/?search=lh%20unit)


## Unidades vw y vh
- [Vh and vw: how and why to use them in Webflow](https://webflow.com/blog/how-and-why-to-use-vh-and-vw-in-webflow)
- [A deep study on CSS Units](https://smazee.com/blog/css-units-px-em-rem-vh-vw-vmin-vmax)
- [Viewport concepts](https://developer.mozilla.org/en-US/docs/Web/CSS/Viewport_concepts)
- [Viewport units CSS. Qué es y cómo utilizar las unidades vh, vw, vmin y vmax](https://www.yunbitsoftware.com/blog/2017/06/22/viewport-units-css-que-es/)
- :zap: [Ejemplo Original (roto)](https://codepen.io/cartuhok/pen/pgwPbP)
- :zap: [Ejemplo con imagen funcionando](https://codepen.io/davidjsalazarmoreno/pen/PoyQqQv)
- :globe_with_meridians: [Soporte vw y vh](https://caniuse.com/?search=vw%20)

## Unidad %
- [A guide to CSS units — pt. 3: percents, viewports, magic and best practices](https://www.peerigon.com/en/blog/complete-guide-to-css-units-series-part-3-percentages-and-viewport-units/)
- [CSS values and units](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Values_and_Units#percentages)
- [percentage](https://developer.mozilla.org/en-US/docs/Web/CSS/percentage)
- [Difference between px, em, %(percentage) in CSS Explained](https://linuxhint.com/difference-between-px-em-percentage-css/)
- [What are CSS percentages?](https://jameshfisher.com/2019/12/29/what-are-css-percentages/)
- [Understanding CSS Percentage](https://dev.to/khangnd/understanding-css-percentage-44gd)
- [A Look Into CSS Units: Pixels, EM, and Percentage](https://www.hongkiat.com/blog/css-units/)
- [CSS percentage unit, the evil parts](https://ismail9k.medium.com/css-percentage-unit-the-evil-parts-e07417a834ef)


## Cheatsheets o chuletas
- [CSS units Cheat Sheet 1](https://www.30secondsofcode.org/articles/s/css-units-cheatsheet/)
- [CSS units Cheat Sheet 2](https://www.raresportan.com/css-units/)
- [CSS Unit Guide: CSS em, rem, vh, vw, and more, Explained](https://www.freecodecamp.org/news/css-unit-guide/)
- [CSS units are confusing AF](https://yurilee.hashnode.dev/css-units-are-confusing-af)
- [A Cheat Sheet for CSS unit](https://dev.to/pffigueiredo/a-cheat-sheet-for-size-units-4cbi)
- [The Lengths of CSS](https://css-tricks.com/the-lengths-of-css/)
- [CSS Units: Which Ones To Use And Avoid](https://medium.com/swlh/css-units-which-ones-to-use-and-avoid-31e4ed461f9)
- [CSS Units Best Practices](https://gist.github.com/basham/2175a16ab7c60ce8e001)
- [CSS Units and their use Cases](https://twitter.com/SauravPurohit4/status/1514119470075179008)

## Guias
- [The ch unit is the most underappreciated CSS unit](https://www.youtube.com/watch?app=desktop&v=dgbFtMBOMlA)
- [Are you using the right CSS units?](https://www.youtube.com/watch?app=desktop&v=N5wpD9Ov_To)
- [A guide to CSS units — pt. 1: look at these absolute units!](https://www.peerigon.com/en/blog/complete-guide-to-css-units-series-part-1-absolute-units/)