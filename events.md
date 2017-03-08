> 此文章是翻译[SyntheticEvent](https://facebook.github.io/react/docs/events.html)这篇React（版本v15.4.0）官方文档。

## SyntheticEvent

SynthecticEvent 入门参考文档包括React 时间系统中的表单部分。参考[Handling Event](https://facebook.github.io/react/docs/handling-events.html) 获取更多信息。

## Overview

你的事件句柄将会传入一个`SyntheticEvent` 实例，一个跨浏览器的包装器包装着浏览器原生事件。它有着和浏览器原生事件相同的接口，包括`stopPropagation()` 和`preventDefault()` ,除了事件同一工作跨所有浏览器。


如果你发现由于某些原因你需要底层浏览器事件，可以简单地使用`nativeEvent` 特性去处理它。每一个`SyntheticEvent` 对象有下面这些特性：
```jsx
boolean bubbles
boolean cancelable
DOMEventTarget currentTarget
boolean defaultPrevented
number eventPhase
boolean isTrusted
DOMEvent nativeEvent
void preventDefault()
boolean isDefaultPrevented()
void stopPropagation()
boolean isPropagationStopped()
DOMEventTarget target
number timeStamp
string type
```
>**Note：**
截至v0.14 这个版本，事件局部中返回`false`不再阻止事件传递。相反，视情况而定手动的触发`e.stopPropagation()` 或者`e.preventDefault()` 方法。

## Event Pooling

`SyntheticEvent` 是一个池子（pooled）。这意味着`SyntheticEvent` 对象是可复用的并且所有的属性在事件函数被调用后都是无效的。这是考虑到性能问题。本身，你不能异步地访问这些事件。
```jsx
  function onClick(event){
    console.log(event); // => nullified object
    console.log(event.type); // => "click"
    const eventType = event.type; // => "click"

    setTimeout(function(){
      console.log(event.type); // => null
      console.log(eventType); // => "click"
    },0);

    // Won't work, this.state.clickEvent will only contain null values
    this.setState({clickEvent: event});

    // You can still export event properties.
    this.setState({eventType: event.type});
  }
```
>**Note：**
如果你想要异步地访问事件属性，你应该调用事件的`event.persist`，这回从池子（pool）中移除合成事件（synthetic event）并且允许引用通过用户代码保存的事件。

## Supported Events

React 标准化事件以至于它们有跨浏览器一致的属性。

下面的事件局部在冒泡阶段（in bubbling phase）被触发。在捕获节点（the capture phase）注册一个事件句柄，追加`Capture` 到事件名称上：例如，替代使用`onClick`，你应该使用`onClickCapture` 在捕获阶段去处理事件。

* [Clipboard Events](https://facebook.github.io/react/docs/events.html#clipboard-events)
* [Composition Events](https://facebook.github.io/react/docs/events.html#composition-events)
* [Keyboard Events](https://facebook.github.io/react/docs/events.html#keyboard-events)
* [Focus Events](https://facebook.github.io/react/docs/events.html#focus-events)
* [Form Events](https://facebook.github.io/react/docs/events.html#form-events)
* [Mouse Events](https://facebook.github.io/react/docs/events.html#,mouse-events)
* [Selection Events](https://facebook.github.io/react/docs/events.html#selection-events)
* [Touch Events](https://facebook.github.io/react/docs/events.html#touch-events)
* [UI Events](https://facebook.github.io/react/docs/events.html#ui-events)
* [Wheel Events](https://facebook.github.io/react/docs/events.html#wheel-events)
* [Media Events](https://facebook.github.io/react/docs/events.html#media-events)
* [Image Events](https://facebook.github.io/react/docs/events.html#image-events)
* [Animation Events](https://facebook.github.io/react/docs/events.html#animation-events)
* [Transition Events](https://facebook.github.io/react/docs/events.html#transition-events)


## Reference

### Clipboard Events

事件名称：
```jsx
onCopy onCute onPaste
```
属性：
```jsx
DOMDataTransfer clipboardData
````

### Composition Events

事件名称：
```jsx
onCompositionEnd onCompositionStart onCompositionUpdate
```
属性：
```jsx
string data
````

### Keyboard Events

事件名称：
```jsx
onKeyDown onKeyPress onKeyUp
```
属性：
```jsx
boolean altKey
number charCode
boolean ctrlKey
boolean getModifierState(key)
string key
number keyCode
string locale
number location
boolean metaKey
boolean repeat
boolean shiftKey
number which
````

### Focus Events

事件名称：
```jsx
onFocus onBlur
```
这些focus event 工作在React DOM 上所有的element，不仅是form element
属性：
```jsx
DOMEventTarget relatedTarget
````

### Form Events

事件名称：
```jsx
onChange onInput onSubmit
```
了解更多关于onChange event，参考[Forms](https://facebook.github.io/react/docs/forms.html)

### Mouse Events

事件名称：
```jsx
onClick onContextMenu onDoubleClick onDrag onDragEnd onDragEnter onDragExit onDragLeave onDragOver onDragStart onDrop onMouseDown onMouseEnter onMouseLeave onMouseMove onMouseOut onMouseOver onMouseUp
```
`onMouseEnter` 和`onMouseLeave` 事件传播是从离开的element 到进入的element而不是任意冒泡并且没有捕获阶段。
属性：
```jsx
boolean altKey
number button
number buttons
number clientX
number clientY
boolean ctrlKey
boolean getModifierState(key)
boolean metaKey
number pageX
number pageY
DOMEventTarget relatedTarget
number screenX
number screenY
boolean shiftKey
````

### Selection Events

事件名称：
```jsx
onSelect
```

### Touch Events

事件名称：
```jsx
onTouchCancel onTouchEnd onTouchMove onTouchStart
```
属性：
```jsx
boolean altKey
DOMTouchList changedTouches
boolean ctrlKey
boolean getModifierState(key)
boolean metaKey
boolean shiftKey
DOMTouchList targetTouches
DOMTouchList touches
````

### UI Events

事件名称：
```jsx
onScroll
```
属性：
```jsx
number detail
DOMAbstractView view
````

### Wheel Events

事件名称：
```jsx
onWheel
```
属性：
```jsx
number deltaMode
number deltaX
number deltaY
number deltaZ
````

### Media Events

事件名称：
```jsx
onAbort onCanPlay onCanPlayThrough onDurationChange onEmptied onEncryted onEnded onError onLoadeData onLoadeMetadata onLoadStart onPause onPlay onPlaying onProgress onRateChange onSeeked onSeeking onStalled onSuspend onTimeUpdate onVolumeChange onWaiting
```

### Image Events

事件名称：
```jsx
onLoad onError
```

### Animation Events

事件名称：
```jsx
onAnimationStart onAnimationEnd onAnimationIteration
```
属性：
```jsx
string animationName
string pseudoElement
float elapsedTime
````

### Transition Events

事件名称：
```jsx
onTransitionEnd
```
属性：
```jsx
string propertyName
string pseudoElement
float elapsedTime
````
