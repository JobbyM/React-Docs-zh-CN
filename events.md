> 此文章是翻译[SyntheticEvent](https://reactjs.org/docs/events.html)这篇React（版本v16.2.0）官方文档。

## SyntheticEvent

入门参考记录React 事件系统中的表单部分的`SynthecticEvent` 包装器。参考[Handling Event](https://reactjs.org/docs/handling-events.html) 获取更多信息。

### Overview

事件处理器将会传入一个`SyntheticEvent` 实例，一个跨浏览器的包装器包装着浏览器原生事件。它有着和浏览器原生事件相同的接口，包括`stopPropagation()` 和`preventDefault()` ,除了这些事件在所有浏览器中的工作相同。


如果你发现由于某些原因你需要底层浏览器事件，可以简单地使用`nativeEvent` 特性去处理它。每一个`SyntheticEvent` 对象有下面这些特性：

```
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
截至v0.14 这个版本，事件处理器中返回`false` 不再阻止事件传递。相反，视情况而定手动的触发`e.stopPropagation()` 或者`e.preventDefault()` 方法。

## Event Pooling

`SyntheticEvent` 是一个池子（pooled）。这意味着`SyntheticEvent` 对象是可复用的并且所有的属性在事件函数被调用后都是无效的。这是考虑到性能问题。因此，你不能异步地访问这些事件。

```
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

>**注意：**
如果你想要异步地访问事件属性，你应该调用事件的`event.persist()`，这会从池子（pool）中移除合成事件（synthetic event）并且允许事件的引用通过用户代码保存。

## Supported Events

React 标准化事件以至于它们有跨浏览器一致的属性。

下面的事件局部在冒泡阶段（in bubbling phase）被触发。在捕获阶段（the capture phase）注册一个事件句柄，追加`Capture` 到事件名称上：例如，替代使用`onClick`，你应该使用`onClickCapture` 在捕获阶段去处理事件。

* [Clipboard Events](https://reactjs.org/docs/events.html#clipboard-events)
* [Composition Events](https://reactjs.org/docs/events.html#composition-events)
* [Keyboard Events](https://reactjs.org/docs/events.html#keyboard-events)
* [Focus Events](https://reactjs.org/docs/events.html#focus-events)
* [Form Events](https://reactjs.org/docs/events.html#form-events)
* [Mouse Events](https://reactjs.org/docs/events.html#,mouse-events)
* [Selection Events](https://reactjs.org/docs/events.html#selection-events)
* [Touch Events](https://reactjs.org/docs/events.html#touch-events)
* [UI Events](https://reactjs.org/docs/events.html#ui-events)
* [Wheel Events](https://reactjs.org/docs/events.html#wheel-events)
* [Media Events](https://reactjs.org/docs/events.html#media-events)
* [Image Events](https://reactjs.org/docs/events.html#image-events)
* [Animation Events](https://reactjs.org/docs/events.html#animation-events)
* [Transition Events](https://reactjs.org/docs/events.html#transition-events)
* [Other Events](https://reactjs.org/docs/events.html#other-events)


## 参考

### Clipboard Events

事件名称：

```
onCopy onCute onPaste
```

属性：

```
DOMDataTransfer clipboardData
```

### Composition Events

事件名称：

```
onCompositionEnd onCompositionStart onCompositionUpdate
```

属性：

```
string data
```

### Keyboard Events

事件名称：

```
onKeyDown onKeyPress onKeyUp
```

属性：

```
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
```

`key` 属性可以接受[DOM级别3事件规范](https://www.w3.org/TR/uievents-key/#named-key-attribute-values)中记录的任何值。

### Focus Events

事件名称：

```
onFocus onBlur
```

这些focus event 工作在React DOM 上所有的元素，不仅是表单元素（form element）

属性：

```
DOMEventTarget relatedTarget
```

### Form Events

事件名称：

```
onChange onInput onSubmit
```

了解更多关于onChange event，参考[Forms](https://facebook.github.io/react/docs/forms.html)

### Mouse Events

事件名称：

```
onClick onContextMenu onDoubleClick onDrag onDragEnd onDragEnter onDragExit onDragLeave onDragOver onDragStart onDrop onMouseDown onMouseEnter onMouseLeave onMouseMove onMouseOut onMouseOver onMouseUp
```

`onMouseEnter` 和`onMouseLeave` 事件传播是从离开的元素 到进入的元素，而不是普通的冒泡，并且没有捕获阶段。

属性：

```
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
```

### Selection Events

事件名称：

```
onSelect
```

### Touch Events

事件名称：

```
onTouchCancel onTouchEnd onTouchMove onTouchStart
```

属性：

```
boolean altKey
DOMTouchList changedTouches
boolean ctrlKey
boolean getModifierState(key)
boolean metaKey
boolean shiftKey
DOMTouchList targetTouches
DOMTouchList touches
```

### UI Events

事件名称：

```
onScroll
```

属性：

```
number detail
DOMAbstractView view
```

### Wheel Events

事件名称：

```
onWheel
```

属性：

```
number deltaMode
number deltaX
number deltaY
number deltaZ
```

### Media Events

事件名称：

```
onAbort onCanPlay onCanPlayThrough onDurationChange onEmptied onEncryted onEnded onError onLoadeData onLoadeMetadata onLoadStart onPause onPlay onPlaying onProgress onRateChange onSeeked onSeeking onStalled onSuspend onTimeUpdate onVolumeChange onWaiting
```

### Image Events

事件名称：

```
onLoad onError
```

### Animation Events

事件名称：

```
onAnimationStart onAnimationEnd onAnimationIteration
```

属性：

```
string animationName
string pseudoElement
float elapsedTime
```

### Transition Events

事件名称：

```
onTransitionEnd
```

属性：

```
string propertyName
string pseudoElement
float elapsedTime
```

### Other Events

事件名称：

```
onToggle
```
