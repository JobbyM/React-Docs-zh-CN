> 此文章是翻译[DOM Elements](https://facebook.github.io/react/docs/dom-elements.html)这篇React（版本v15.5.4）官方文档。

## DOM Elements

React 为了性能和跨浏览器兼容实现了一个独立的DOM 系统。我们有机会去除一些粗糙的浏览器DOM实现。

在React 中，所有的DOM 属性（properties）和特性（attributes）（包括事件句柄）都应该是camelCased。例如，HTML 特性`tabindex` 对于React 特性`tabIndex`。特例是`aria-*` 和`data-*` 特性，它们应该是lowercased。

## Differences In Attributes

在React 和HTML 中有许多特性的工作是不同的：

### checked

`checkbox` 或`radio` 类型的`<input>` component 是支持`checked` 特性。你可以使用它用来设置一个component 是否被选中（checked）。这对于构建可控的component 是有用的。`defaultChecked` 等同于不可控的，用来设置一个component 首次加载时是否被选中。

### className

配置CSS class，使用`className` 特性。这应用在所有的常规的DOM 和SVG 的元素像`<div>`，`<a>` 以及其它元素。

如果你在Web Component （这并不常用）中使用React，使用`class` 特性来替代。

### dangerouslySetInnerHTML

`dangerouslySetInnerHTML` 是React 中用来替代浏览器DOM 中的`innerHTML`。通常，在代码中设置HTML 是危险的因为它很容易无意的暴露你的用户，遭受到[cross-site scripting(XSS)](https://en.wikipedia.org/wiki/Cross-site_scripting)攻击。所以，你可以在React 中直接设置HTML，但是你必须使用`dangerouslySetInnerHTML` 并传递一个代码`__html` key 的对象，用来提醒你这是危险的。例如：
```jsx
function createMarkup(){
  return {__html: 'First &middot; Second'}
}

function MyComponent(){
  return <div dangerouslySetInnerHTML={createMarkup()} />
}
```

### htmlFor

由于`for` 是JavaScript 中的保留字，所以React element 使用`htmlFor` 来替代。

### onChange

`onChange` 事件行为就如果你期待的那样：无论何时一个表单域改变了，这个事件都会触发。我们有意不使用已经存在的浏览器行为因为`onChange` 这个名称同它的行为是不匹配的，React 依赖这个事件去实时控制用户的输入。

### selected

`<option>` component 支持`selected` 特性。你可以使用它去设置component 是否被选中。这对构建可控的component 是有用的。

### style

`stye` 特性接受一个camelCased 的JavaScript 对象而不是一个CSS 字符串。这同DOM 的style 这个JavaScript 属性是一致的，是更有效的，并且阻止了XSS 安全漏洞。例如：
```jsx
const divStryle = {
  color: 'blue',
  backgroundImage: 'url(' + imgUrl + ')'
}

function HelloWorldComponent(){
  return <div style={divStyle}>Hello World!</div>
}
```
注意到style 并不是自动加前缀的。为了支持老版本浏览器，你需要补充相应的浏览器style 属性：
```jsx
const divStyle = {
  WebkitTransition: 'all', // note the capital 'W' here
  msTransition: 'all' // 'ms' is ht only lowercase vendor prefix
}
function ComponentWithTransition(){
  return <div style={divStyle}>This should work cross-browser</div>
}
```
Style keys 是camelCased 为了保持同JS 访问DOM node上的属性一致（例如，`node.style.backgroundImage`）。厂商（vendor）前缀[除了`ms`](http://www.andismith.com/blog/2012/02/modernizr-prefixed/)都应该是大写字母开头。这就是为什么`WebkitTransition` 是以`W`开头。

### suppressContentEditableWarning

通常，当有孩子的element 被标记为`contentEditable` 时，会发出警告，因为它不起作用。这个特性就是为了阻止这个警告。除非你正在构建一个类似[Draft.js](https://facebook.github.io/draft-js/) 的库用来手动管理`contentEditable`，否则不要使用它。

### value

`<input>`和`<textarea>` component 支持`value` 特性。你可以用它设置component 的value。这对构建可控的component 是有用的。 `defaultValued` 等同于不可控的，用来设置一个component 首次加载时的value。

## All Supported HTML Attributes

React 支持所有的`data-*`和`aria-*` 特性，还有下面这些特性：
```jsx
accept acceptCharset accessKey action allowFullScreen allowTransparency alt
async autoComplete autoFocus autoPlay capture cellPadding cellSpacing challenge
charSet checked cite classID className colSpan cols content contentEditable
contextMenu controls coords crossOrigin data dateTime default defer dir
disabled download draggable encType form formAction formEncType formMethod
formNoValidate formTarget frameBorder headers height hidden high href hrefLang
htmlFor httpEquiv icon id inputMode integrity is keyParams keyType kind label
lang list loop low manifest marginHeight marginWidth max maxLength media
mediaGroup method min minLength multiple muted name noValidate nonce open
optimum pattern placeholder poster preload profile radioGroup readOnly rel
required reversed role rowSpan rows sandbox scope scoped scrolling seamless
selected shape size sizes span spellCheck src srcDoc srcLang srcSet start step
style summary tabIndex target title type useMap value width wmode wrap
```
这些RDFa 特性是被支持的（几个同标准的HTML 特性重复的RDFa特性是排除在这个列表的）：
```jsx
about datatype inlist prefix property resource typeof vocab
```
除此之外，下面这些非标准的特性也是被支持的：

* Mobile Safari 中的`autoCapitalize autoCorrect`
* Safari 中的为`<link ref="mask-icon" />` 这种`color`
* [HTML5 mircodata](http://schema.org/docs/gs.html) 中的`itemProp itemScope itemType itemRef itemID`
* 老版本IE浏览器中的`security`
* IE 浏览器中的`unselectable`
* 对WebKit/Blink 中的`search` 类型输入域中的`results autoSave`

## All Supported SVG Attributes

React 支持这些SVG 特性：
```jsx
accentHeight accumulate additive alignmentBaseline allowReorder alphabetic
amplitude arabicForm ascent attributeName attributeType autoReverse azimuth
baseFrequency baseProfile baselineShift bbox begin bias by calcMode capHeight
clip clipPath clipPathUnits clipRule colorInterpolation
colorInterpolationFilters colorProfile colorRendering contentScriptType
contentStyleType cursor cx cy d decelerate descent diffuseConstant direction
display divisor dominantBaseline dur dx dy edgeMode elevation enableBackground
end exponent externalResourcesRequired fill fillOpacity fillRule filter
filterRes filterUnits floodColor floodOpacity focusable fontFamily fontSize
fontSizeAdjust fontStretch fontStyle fontVariant fontWeight format from fx fy
g1 g2 glyphName glyphOrientationHorizontal glyphOrientationVertical glyphRef
gradientTransform gradientUnits hanging horizAdvX horizOriginX ideographic
imageRendering in in2 intercept k k1 k2 k3 k4 kernelMatrix kernelUnitLength
kerning keyPoints keySplines keyTimes lengthAdjust letterSpacing lightingColor
limitingConeAngle local markerEnd markerHeight markerMid markerStart
markerUnits markerWidth mask maskContentUnits maskUnits mathematical mode
numOctaves offset opacity operator order orient orientation origin overflow
overlinePosition overlineThickness paintOrder panose1 pathLength
patternContentUnits patternTransform patternUnits pointerEvents points
pointsAtX pointsAtY pointsAtZ preserveAlpha preserveAspectRatio primitiveUnits
r radius refX refY renderingIntent repeatCount repeatDur requiredExtensions
requiredFeatures restart result rotate rx ry scale seed shapeRendering slope
spacing specularConstant specularExponent speed spreadMethod startOffset
stdDeviation stemh stemv stitchTiles stopColor stopOpacity
strikethroughPosition strikethroughThickness string stroke strokeDasharray
strokeDashoffset strokeLinecap strokeLinejoin strokeMiterlimit strokeOpacity
strokeWidth surfaceScale systemLanguage tableValues targetX targetY textAnchor
textDecoration textLength textRendering to transform u1 u2 underlinePosition
underlineThickness unicode unicodeBidi unicodeRange unitsPerEm vAlphabetic
vHanging vIdeographic vMathematical values vectorEffect version vertAdvY
vertOriginX vertOriginY viewBox viewTarget visibility widths wordSpacing
writingMode x x1 x2 xChannelSelector xHeight xlinkActuate xlinkArcrole
xlinkHref xlinkRole xlinkShow xlinkTitle xlinkType xmlBase xmlLang xmlSpace
y y1 y2 yChannelSelector z zoomAndPan
```
