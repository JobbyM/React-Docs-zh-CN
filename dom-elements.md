> 此文章是翻译[DOM Elements](https://reactjs.org/docs/dom-elements.html)这篇React（版本v16.2.0）官方文档。

## DOM Elements

React 为了性能和跨浏览器兼容实现了一个浏览器无关的DOM 系统。我们有机会去除一些粗糙的浏览器DOM实现。

在React 中，所有的DOM 属性（properties）和特性（attributes）（包括事件句柄）都应该是camelCase。例如，HTML 特性`tabindex` 对于React 特性`tabIndex`。特例是`aria-*` 和`data-*` 特性，它们应该是lowercase。

### Differences In Attributes

在React 和HTML 中有许多特性的工作是不同的：

#### checked

`checkbox` 或`radio` 类型的`<input>` 组件是支持`checked` 特性。你可以使用它用来设置一个组件是否被选中（checked）。这对于构建可控的组件是有用的。`defaultChecked` 等同于不可控的，用来设置一个component 首次加载时是否被选中。

#### className

配置CSS 类，使用`className` 特性。这应用在所有的常规的DOM 和SVG 的元素像`<div>`，`<a>` 以及其它元素。

如果你在Web Component （这并不常用）中使用React，使用`class` 特性来替代。

#### dangerouslySetInnerHTML

`dangerouslySetInnerHTML` 是React 中用来替代浏览器DOM 中的`innerHTML`。通常，在代码中设置HTML 是危险的因为它很容易无意的暴露你的用户，遭受到[cross-site scripting(XSS)](https://en.wikipedia.org/wiki/Cross-site_scripting)攻击。所以，你可以在React 中直接设置HTML，但是你必须使用`dangerouslySetInnerHTML` 并传递一个代码`__html` key 的对象，用来提醒你这是危险的。例如：

```
function createMarkup(){
  return {__html: 'First &middot; Second'}
}

function MyComponent(){
  return <div dangerouslySetInnerHTML={createMarkup()} />
}
```

#### htmlFor

由于`for` 是JavaScript 中的保留字，所以React element 使用`htmlFor` 来替代。

#### onChange

`onChange` 事件行为就如果你期待的那样：无论何时一个表单域改变了，这个事件都会触发。我们有意不使用已经存在的浏览器行为因为`onChange` 这个名称同它的行为是不匹配的，React 依赖这个事件去实时控制用户的输入。

#### selected

`<option>` 组件支持`selected` 特性。你可以使用它去设置组件是否被选中。这对构建可控的component 是有用的。

#### style

> 注意
> 文档中的一些示例使用`style` 是为了方便，但 **通常不推荐使用`style` 特性作为样式元素的主要方法**。在大多数情况下，(类名称className)[https://reactjs.org/docs/dom-elements.html#classname]应该用于引用外部CSS样式表中定义的类。`style` 最常用于React 应用程序，以便在渲染时添加动态计算的样式。请参考[FAQ:Stying and CSS](https://reactjs.org/docs/faq-styling.html)。

`style` 特性接受一个camelCased 形式属性的JavaScript 对象而不是一个CSS 字符串。这同DOM 的`style` JavaScript 属性是一致的，是更有效的，并且阻止了XSS 安全漏洞。例如：

```
const divStryle = {
  color: 'blue',
  backgroundImage: 'url(' + imgUrl + ')'
}

function HelloWorldComponent(){
  return <div style={divStyle}>Hello World!</div>
}
```

注意到style 并不是自动加前缀的。为了支持老版本浏览器，你需要补充相应的浏览器style 属性：

```
const divStyle = {
  WebkitTransition: 'all', // note the capital 'W' here
  msTransition: 'all' // 'ms' is the only lowercase vendor prefix
}
function ComponentWithTransition(){
  return <div style={divStyle}>This should work cross-browser</div>
}
```

Style keys 是camelCased 为了保持同JS 访问DOM 节点上的属性一致（例如，`node.style.backgroundImage`）。厂商（vendor）前缀[除了`ms`](http://www.andismith.com/blog/2012/02/modernizr-prefixed/)都应该是大写字母开头。这就是为什么`WebkitTransition` 是以`W`开头。

React 会自动将“px” 后缀附加到某些数值内联样式属性中。如果你想使用除“px” 以外的单位，请将值指定为具有所需单元的字符串。例如:

{% raw %}
```
// Result style: '10px'
<div style={{ height: 10 }}>
  Hello World!
</div>

// Result style: '10p%'
<div style={{ height: '10p%' }}>
  Hello World!
</div>
```
{% endraw %}

并不是所有样式属性都被转换成像素字符串。某些样式无单位（如缩放`zoom`、`order`、`flex`）。一个完整的无单位列表可以在[这里看到](https://github.com/facebook/react/blob/4131af3e4bf52f3a003537ec95a1655147c81270/src/renderers/dom/shared/CSSProperty.js#L15-L59)。

#### suppressContentEditableWarning

通常，当有子节点的元素被标记为`contentEditable` 时，会发出警告，因为它不起作用。这个特性就是为了阻止这个警告。除非你正在构建一个类似[Draft.js](https://facebook.github.io/draft-js/) 的库用来手动管理`contentEditable`，否则不要使用它。

#### suppressHydrationWarning

如果你使用服务器端React 渲染，通常在服务器和客户端呈现不同内容时会出现警告。然而，在一些罕见的情况下，要保证精确匹配是非常困难或不可能的。例如，时间戳（timestamps）期许是不同的，在服务器和客户端上。

如果你设置`suppresshydrationwarning` 为`true`，React 将不会警告你关于元素的内容和属性是不匹配。它只工作一级深，是用来作为逃生舱口。不要过度使用它。你可以在[ReactDOM.hydrate() 文档](https://reactjs.org/docs/react-dom.html#hydrate)阅读更多关于`hydration` 。

#### value

`<input>`和`<textarea>` 组件支持`value` 特性。你可以用它设置组件的值。这对构建可控的组件是有用的。 `defaultValued` 等同于不可控的，用来设置一个组件首次加载时的值。

## All Supported HTML Attributes

在React 16中，完全支持任何标准的或[自定义](https://reactjs.org/blog/2017/09/08/dom-attributes-in-react-16.html) 的DOM 特性。

React 始终为DOM 提供了一个以JavaScript 为中心的API。由于React 组件经常同时使用自定义的和 DOM 相关的props，所以React 使用Camelcase 约定，就像DOM API一样：

```
<div tabIndex="-1" />        // Just like node.tabIndex DOM API
<div className="Buttton" />  // Just like node.className DOM API
<input readOnly={true} />    // Just like node.readOnly DOM API
```

这些props 的工作类似于相应的HTML 特性，除了上述文档特殊的情况。

React 支持的一些DOM 特性，包括：

```
accept acceptCharset accessKey action allowFullScreen alt async autoComplete
autoFocus autoPlay capture cellPadding cellSpacing challenge charSet checked
cite classID className colSpan cols content contentEditable contextMenu controls
controlsList coords crossOrigin data dateTime default defer dir disabled
download draggable encType form formAction formEncType formMethod formNoValidate
formTarget frameBorder headers height hidden high href hrefLang htmlFor
httpEquiv icon id inputMode integrity is keyParams keyType kind label lang list
loop low manifest marginHeight marginWidth max maxLength media mediaGroup method
min minLength multiple muted name noValidate nonce open optimum pattern
placeholder poster preload profile radioGroup readOnly rel required reversed
role rowSpan rows sandbox scope scoped scrolling seamless selected shape size
sizes span spellCheck src srcDoc srcLang srcSet start step style summary
tabIndex target title type useMap value width wmode wrap
```

类似地，完全支持所有SVG属性：

```
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
xlinkHref xlinkRole xlinkShow xlinkTitle xlinkType xmlns xmlnsXlink xmlBase
xmlLang xmlSpace y y1 y2 yChannelSelector z zoomAndPan
```

你也可以使用自定义特性，只要它们完全是小写的。
