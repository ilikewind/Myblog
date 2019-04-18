---
layout: post
title: "css那么多属性，其较好的书写顺序是怎么样的呢？"
subtitle: ""
catalog: false
author: "WangW"
header-style: text
tags: 
    - web
---

CSS的本质可以分为宏观与微观两方面。 宏观上它的存在就是为了控制页面的显示样式。包括布局，颜色，字体等。微观上则是实现这种控制功能的各种属性的定义和工作原理。

了解定义就能干活，知道原理才能把活干好。

题主说属性太多，其实CSS就是去控制样式而已，网页样式是借鉴于传统的报纸等印刷品的排版。你随便在身边找一份报纸或者杂志的一页，用CSS尽可能的还原出来。整体布局还原出来问题应该不大，但是具体到细节的时候可能会有很多问题，比如出来的效果总是跟想的不一样。这个时候就该去看看单个属性的工作原理，比如margin是用来控制外边距的但是用%的时候它是怎么计算的最终值呢？当发现出乎意料的时候就去

[Index](https://www.w3.org/TR/2011/REC-CSS2-20110607/indexlist.html)查一下属性的定义和值的计算方法。

常用基础样式：

![css基础样式图](/img/in-post/2019/css.jpg)

Tips：

- 常用CSS属性margin和padding的%取值是基于包含块（离元素最近的块级祖先元素）的宽,注意是宽。
- 行内元素相关的内容区、行内框、基线这几个概念很重要。
-  inline-block是，内部被解析为块级元素，自身被解析为行内元素。
-  background-image可以同时为一个元素设置多个背景图配合background-position可以组合出神奇的效果。
- 浮动的元素会消除外边距重合，浮动的元素会被解析为块级元素。
- top,right,bottom,left,z-index这些属性只有用在定位元素(除了position为static值)上才有效。
- overflow的值设置为scroll，在移动端可以做横向滑动效果。
- 除了做表格不要用table布局。
-  选择器都很简单，只说一类。结构伪类选择器，原理可以理解为先找到符合条件的元素后，向上找到其父元素然后验证规则。

---

CSS 属性推荐书写顺序：

缩减总结版：[参考](https://blog.csdn.net/zzp961224/article/details/54799604)

1. 位置属性(position, top, right, z-index, display, float等)
2. 大小(width, height, padding, margin)
3. 文字系列(font, line-height, letter-spacing, color- text-align等)
4. 背景(background, border等)
5. 其他(animation, transition等)



```
div {  
  position: ;
  top: ;
  right: ;
  bottom: ;
  left: ;
  z-index: ;
  float: ;
  display: ;
  width: ;
  height: ;
  max-width: ;
  max-height: ;
  min-width: ;
  min-height: ;
  padding: ;
  padding-top: ;
  padding-right: ;
  padding-bottom: ;
  padding-left: ;
  border: ;
  border-collapse: ;
  border-top: ;
  border-right: ;
  border-bottom: ;
  border-left: ;
  border-color: ;
  border-image: ;
  border-top-color: ;
  border-right-color: ;
  border-bottom-color: ;
  border-left-color: ;
  border-spacing: ;
  border-style: ;
  border-top-style: ;
  border-right-style: ;
  border-bottom-style: ;
  border-left-style: ;
  border-width: ;
  border-top-width: ;
  border-right-width: ;
  border-bottom-width: ;
  border-left-width: ;
  border-radius: ;
  border-top-right-radius: ;
  border-bottom-right-radius: ;
  border-bottom-left-radius: ;
  border-top-left-radius: ;
  margin: ;
  margin-top: ;
  margin-right: ;
  margin-bottom: ;
  margin-left: ;
  overflow: ;
  overflow-x: ;
  overflow-y: ;
  clip: ;
  clear: ;
  font: ;
  font-family: ;
  font-size: ;
  font-style: ;
  font-weight: ;
  src: ;
  line-height: ;
  letter-spacing: ;
  word-spacing: ;
  color: ;
  text-align: ;
  text-decoration: ;
  text-indent: ;
  text-overflow: ;
  text-rendering: ;
  text-shadow: ;
  text-transform: ;
  word-break: ;
  word-wrap: ;
  white-space: ;
  vertical-align: ;
  list-style: ;
  list-style-type: ;
  list-style-position: ;
  list-style-image: ;
  pointer-events: ;
  cursor: ;
  background: ;
  background-attachment: ;
  background-color: ;
  background-image: ;
  background-position: ;
  background-repeat: ;
  background-size: ;
  content: ;
  quotes: ;
  outline: ;
  outline-offset: ;
  opacity: ;
  filter: ;
  visibility: ;
  size: ;
  zoom: ;
  transform: ;
  box-shadow: ;
  box-sizing: ;
  table-layout: ;
  animation: ;
  animation-delay: ;
  animation-duration: ;
  animation-iteration-count: ;
  animation-name: ;
  animation-play-state: ;
  animation-timing-function: ;
  animation-fill-mode: ;
  transition: ;
  transition-delay: ;
  transition-duration: ;
  transition-property: ;
  transition-timing-function: ;
  background-clip: ;
  backface-visibility: ;
  resize: ;
  direction: ;
  page: ;
  unicode-bidi: ;
  align-content: ;
  align-items: ;
  align-self: ;
  alignment-baseline: ;
  all: ;
  animation-direction: ;
  background-blend-mode: ;
  background-origin: ;
  background-position-x: ;
  background-position-y: ;
  background-repeat-x: ;
  background-repeat-y: ;
  baseline-shift: ;
  border-image-outset: ;
  border-image-repeat: ;
  border-image-slice: ;
  border-image-source: ;
  border-image-width: ;
  buffered-rendering: ;
  caption-side: ;
  clip-path: ;
  clip-rule: ;
  color-interpolation: ;
  color-interpolation-filters: ;
  color-rendering: ;
  counter-increment: ;
  counter-reset: ;
  cx: ;
  cy: ;
  dominant-baseline: ;
  empty-cells: ;
  fill: ;
  fill-opacity: ;
  fill-rule: ;
  flex: ;
  flex-basis: ;
  flex-direction: ;
  flex-flow: ;
  flex-grow: ;
  flex-shrink: ;
  flex-wrap: ;
  flood-color: ;
  flood-opacity: ;
  font-feature-settings: ;
  font-kerning: ;
  font-stretch: ;
  font-variant: ;
  font-variant-ligatures: ;
  image-rendering: ;
  isolation: ;
  justify-content: ;
  lighting-color: ;
  marker: ;
  marker-end: ;
  marker-mid: ;
  marker-start: ;
  mask: ;
  mask-type: ;
  max-zoom: ;
  min-zoom: ;
  mix-blend-mode: ;
  motion: ;
  motion-offset: ;
  motion-path: ;
  motion-rotation: ;
  object-fit: ;
  object-position: ;
  order: ;
  orientation: ;
  orphans: ;
  outline-color: ;
  outline-style: ;
  outline-width: ;
  overflow-wrap: ;
  page-break-after: ;
  page-break-before: ;
  page-break-inside: ;
  paint-order: ;
  perspective: ;
  perspective-origin: ;
  r: ;
  rx: ;
  ry: ;
  shape-image-threshold: ;
  shape-margin: ;
  shape-outside: ;
  shape-rendering: ;
  speak: ;
  stop-color: ;
  stop-opacity: ;
  stroke: ;
  stroke-dasharray: ;
  stroke-dashoffset: ;
  stroke-linecap: ;
  stroke-linejoin: ;
  stroke-miterlimit: ;
  stroke-opacity: ;
  stroke-width: ;
  tab-size: ;
  text-align-last: ;
  text-anchor: ;
  text-combine-upright: ;
  text-orientation: ;
  touch-action: ;
  transform-origin: ;
  transform-style: ;
  unicode-range: ;
  user-zoom: ;
  vector-effect: ;
  webkit-app-region: ;
  webkit-appearance: ;
  webkit-background-clip: ;
  webkit-background-composite: ;
  webkit-background-origin: ;
  webkit-border-after: ;
  webkit-border-after-color: ;
  webkit-border-after-style: ;
  webkit-border-after-width: ;
  webkit-border-before: ;
  webkit-border-before-color: ;
  webkit-border-before-style: ;
  webkit-border-before-width: ;
  webkit-border-end: ;
  webkit-border-end-color: ;
  webkit-border-end-style: ;
  webkit-border-end-width: ;
  webkit-border-horizontal-spacing: ;
  webkit-border-image: ;
  webkit-border-start: ;
  webkit-border-start-color: ;
  webkit-border-start-style: ;
  webkit-border-start-width: ;
  webkit-border-vertical-spacing: ;
  webkit-box-align: ;
  webkit-box-decoration-break: ;
  webkit-box-direction: ;
  webkit-box-flex: ;
  webkit-box-flex-group: ;
  webkit-box-lines: ;
  webkit-box-ordinal-group: ;
  webkit-box-orient: ;
  webkit-box-pack: ;
  webkit-box-reflect: ;
  webkit-clip-path: ;
  webkit-column-break-after: ;
  webkit-column-break-before: ;
  webkit-column-break-inside: ;
  webkit-column-count: ;
  webkit-column-gap: ;
  webkit-column-rule: ;
  webkit-column-rule-color: ;
  webkit-column-rule-style: ;
  webkit-column-rule-width: ;
  webkit-column-span: ;
  webkit-column-width: ;
  webkit-columns: ;
  webkit-filter: ;
  webkit-font-size-delta: ;
  webkit-font-smoothing: ;
  webkit-highlight: ;
  webkit-hyphenate-character: ;
  webkit-line-break: ;
  webkit-line-clamp: ;
  webkit-locale: ;
  webkit-logical-height: ;
  webkit-logical-width: ;
  webkit-margin-after: ;
  webkit-margin-after-collapse: ;
  webkit-margin-before: ;
  webkit-margin-before-collapse: ;
  webkit-margin-bottom-collapse: ;
  webkit-margin-collapse: ;
  webkit-margin-end: ;
  webkit-margin-start: ;
  webkit-margin-top-collapse: ;
  webkit-mask: ;
  webkit-mask-box-image: ;
  webkit-mask-box-image-outset: ;
  webkit-mask-box-image-repeat: ;
  webkit-mask-box-image-slice: ;
  webkit-mask-box-image-source: ;
  webkit-mask-box-image-width: ;
  webkit-mask-clip: ;
  webkit-mask-composite: ;
  webkit-mask-image: ;
  webkit-mask-origin: ;
  webkit-mask-position: ;
  webkit-mask-position-x: ;
  webkit-mask-position-y: ;
  webkit-mask-repeat: ;
  webkit-mask-repeat-x: ;
  webkit-mask-repeat-y: ;
  webkit-mask-size: ;
  webkit-max-logical-height: ;
  webkit-max-logical-width: ;
  webkit-min-logical-height: ;
  webkit-min-logical-width: ;
  webkit-padding-after: ;
  webkit-padding-before: ;
  webkit-padding-end: ;
  webkit-padding-start: ;
  webkit-perspective-origin-x: ;
  webkit-perspective-origin-y: ;
  webkit-print-color-adjust: ;
  webkit-rtl-ordering: ;
  webkit-ruby-position: ;
  webkit-tap-highlight-color: ;
  webkit-text-combine: ;
  webkit-text-decorations-in-effect: ;
  webkit-text-emphasis: ;
  webkit-text-emphasis-color: ;
  webkit-text-emphasis-position: ;
  webkit-text-emphasis-style: ;
  webkit-text-fill-color: ;
  webkit-text-orientation: ;
  webkit-text-security: ;
  webkit-text-stroke: ;
  webkit-text-stroke-color: ;
  webkit-text-stroke-width: ;
  webkit-transform-origin-x: ;
  webkit-transform-origin-y: ;
  webkit-transform-origin-z: ;
  webkit-user-drag: ;
  webkit-user-modify: ;
  webkit-user-select: ;
  webkit-writing-mode: ;
  widows: ;
  will-change: ;
  writing-mode: ;
  x: ;
  y: ;
  css-text: ;
  length: ;
  parent-rule: ;
  css-float: ;
  item: ;
  get-property-value: ;
  get-property-priority: ;
  set-property: ;
  remove-property: ;
}
```

----

作者：韩双力

链接：https://www.zhihu.com/question/31317160/answer/85833065

来源：知乎

著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。