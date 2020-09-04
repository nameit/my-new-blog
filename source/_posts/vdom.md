title: 简易虚拟dom原理
tags: [vdom]
categories: 前端
---

## 虚拟dom的样子

1、原生dom

```html
<div class="outer" id="app">
    <div class="inner">内部</div>
</div>
```

2、虚拟dom

```js
vnode = {
    tag: 'div',
    data: {
        class: 'outer',
        id: 'app'
    },
    children: [{
        tag: 'div',
        data: {
            class: 'inner'
        },
        children: '内部'
    }]
}
```



## 什么是虚拟dom

为了避免过多操作原生dom，用js模拟dom树结构的一个对象。



## js操作原生dom为什么开销大

> 因为浏览器渲染dom树的引擎和 js引擎是分开的，如果通过js操作dom这些跨引擎的通讯增加了成本，以及 dom 操作引起的浏览器的回流和重绘，使得性能开销巨大，原本在 pc 端是没有性能问题的，因为 pc 的计算能力强，但是随着移动端的发展，越来越多的网页在智能手机上运行，而手机的性能参差不齐，会有性能问题。

- 随便新建一个div，它的属性就有如下这么多：

  ```js
  "align, title, lang, translate, dir, hidden, accessKey, draggable, spellcheck, autocapitalize, contentEditable, isContentEditable, inputMode, offsetParent, offsetTop, offsetLeft, offsetWidth, offsetHeight, style, innerText, outerText, oncopy, oncut, onpaste, onabort, onblur, oncancel, oncanplay, oncanplaythrough, onchange, onclick, onclose, oncontextmenu, oncuechange, ondblclick, ondrag, ondragend, ondragenter, ondragleave, ondragover, ondragstart, ondrop, ondurationchange, onemptied, onended, onerror, onfocus, oninput, oninvalid, onkeydown, onkeypress, onkeyup, onload, onloadeddata, onloadedmetadata, onloadstart, onmousedown, onmouseenter, onmouseleave, onmousemove, onmouseout, onmouseover, onmouseup, onmousewheel, onpause, onplay, onplaying, onprogress, onratechange, onreset, onresize, onscroll, onseeked, onseeking, onselect, onstalled, onsubmit, onsuspend, ontimeupdate, ontoggle, onvolumechange, onwaiting, onwheel, onauxclick, ongotpointercapture, onlostpointercapture, onpointerdown, onpointermove, onpointerup, onpointercancel, onpointerover, onpointerout, onpointerenter, onpointerleave, onselectstart, onselectionchange, dataset, nonce, tabIndex, click, focus, blur, enterKeyHint, onformdata, onpointerrawupdate, attachInternals, namespaceURI, prefix, localName, tagName, id, className, classList, slot, part, attributes, shadowRoot, assignedSlot, innerHTML, outerHTML, scrollTop, scrollLeft, scrollWidth, scrollHeight, clientTop, clientLeft, clientWidth, clientHeight, attributeStyleMap, onbeforecopy, onbeforecut, onbeforepaste, onsearch, previousElementSibling, nextElementSibling, children, firstElementChild, lastElementChild, childElementCount, onfullscreenchange, onfullscreenerror, onwebkitfullscreenchange, onwebkitfullscreenerror, setPointerCapture, releasePointerCapture, hasPointerCapture, hasAttributes, getAttributeNames, getAttribute, getAttributeNS, setAttribute, setAttributeNS, removeAttribute, removeAttributeNS, hasAttribute, hasAttributeNS, toggleAttribute, getAttributeNode, getAttributeNodeNS, setAttributeNode, setAttributeNodeNS, removeAttributeNode, closest, matches, webkitMatchesSelector, attachShadow, getElementsByTagName, getElementsByTagNameNS, getElementsByClassName, insertAdjacentElement, insertAdjacentText, insertAdjacentHTML, requestPointerLock, getClientRects, getBoundingClientRect, scrollIntoView, scroll, scrollTo, scrollBy, scrollIntoViewIfNeeded, animate, computedStyleMap, before, after, replaceWith, remove, prepend, append, querySelector, querySelectorAll, requestFullscreen, webkitRequestFullScreen, webkitRequestFullscreen, createShadowRoot, getDestinationInsertionPoints, elementTiming, ELEMENT_NODE, ATTRIBUTE_NODE, TEXT_NODE, CDATA_SECTION_NODE, ENTITY_REFERENCE_NODE, ENTITY_NODE, PROCESSING_INSTRUCTION_NODE, COMMENT_NODE, DOCUMENT_NODE, DOCUMENT_TYPE_NODE, DOCUMENT_FRAGMENT_NODE, NOTATION_NODE, DOCUMENT_POSITION_DISCONNECTED, DOCUMENT_POSITION_PRECEDING, DOCUMENT_POSITION_FOLLOWING, DOCUMENT_POSITION_CONTAINS, DOCUMENT_POSITION_CONTAINED_BY, DOCUMENT_POSITION_IMPLEMENTATION_SPECIFIC, nodeType, nodeName, baseURI, isConnected, ownerDocument, parentNode, parentElement, childNodes, firstChild, lastChild, previousSibling, nextSibling, nodeValue, textContent, hasChildNodes, getRootNode, normalize, cloneNode, isEqualNode, isSameNode, compareDocumentPosition, contains, lookupPrefix, lookupNamespaceURI, isDefaultNamespace, insertBefore, appendChild, replaceChild, removeChild, addEventListener, removeEventListener, dispatchEvent, "
  ```

- [重绘和回流]( https://segmentfault.com/a/1190000017329980 )（重排）

   ![webkit渲染过程](https://image-static.segmentfault.com/408/885/4088852130-5afbe6c95934b_articlex) 

  

  如果重排就会改变上面那么多属性的值

  

## 虚拟dom的新建

### 新建标签节点的虚拟dom

```js
createVnodeElement(tag, data, children = null) {
	let flag;
    if (typeof tag === 'string') {
        flag = 'html'
    } else if (typeof tag === 'function') {
        flag = 'component'
    } else {
        flag = 'text'
    }
    let childrenFlag;
    if(children === null) {
        childrenFlag = 'empty'
    } else if (Array.isArray(children)) {
        let length = children.length;
        if (length === 0) {
            childrenFlag = 'empty'
        } else {
            childrenFlag = 'multiple'
        }
    } else {
        childrenFlag = 'single';
        children = createTextVnode(children + '');
    }
    
    return {
        flag,
        tag,
        data,
        children,
        childrenFlag
    }
}
```
### 新建文本节点的虚拟dom
```js
// 新建文本类型虚拟dom
function createTextVnode(text) {
  return {
    flag: vnodeType.TEXT,
    tag: null,
    data: null,
    children: text,
    childrenFlag: childType.EMPTY
  }
}
```


## 虚拟dom的调用

```js
vnode = createElement('div', {id: 'test'}, [
      createElement(123)
      createElement('p', {key: 'a', style: {color: 'blue'}}, '节点1'),
      createElement('p', {key: 'b', '@click': () => {alert(xx)}}, '节点2'),
      createElement('p', {key: 'c', 'class': 'item-header'}, '节点3'),
      createElement('p', {key: 'd'}, '节点4')
    ])
```



## 虚拟dom的渲染

```js
render(vnode, document.getElementById('app'))
```

```js
// 渲染
function render(vnode, container) {
    if (container.vnode) {
       patch(container.vnode, vnode, container)
    } else {
  	   mount(vnode, container)
    }
    container.vnode = vnode;
}

// 首次挂载元素
function mount(vnode, container) {
  let {flag} = vnode
  // 标签节点
  if (flag === 'html') {
    mountElement(vnode, container)
  // 文本节点
  } else if (flag === 'text') {
    mountText(vnode, container)
  }
}
```



### 标签节点的属性挂载

```js
if (data) {
    for (let key in data) {
        // 节点，名字，老值，新值
        patchData(dom, key, null, data[key])
    }
}
```



### 标签节点的子元素挂载

```js
if (childrenFlag !== childType.EMPTY) {
    if (childrenFlag === childType.SINGLE) {
        mount(children, dom)
    } else if (childrenFlag === childType.MULTIPLE) {
        for (let i = 0; i < children.length; i++) {
            mount(children[i], dom)
        }
    }
}
```





## 虚拟dom核心：diff

这是另一位同学分享的[虚拟dom的diff算法浅析]( http://10.199.140.56/book/part1/react/diff.html )



- 节点分标签节点和文本节点，所以需要flag来判断
- childrenFlag判断不同的子元素
- 判断后进行不同形式的渲染
- 难点：diff两个数组，例子：[axxbxxc] ->  [cxxaxxb] ，虚拟dom从axxbxxc变为cxxaxxb，c一开始不需要移动位置，只管插入dom中，ab俩是按字母顺序所以b就不需要移动位置，但a到c后面了不是按字母顺序，所以需要移动位置。
- [abcd] -> [acd]，react或vue的key对应list数组里每个元素，如果删掉一个b，还用index下标作为key的话，c会取代原来b的位置，这时候如果是选中c,效果就会变成d被选中，所以尽量不要用index作为key。

[简易虚拟dom源码](https://github.com/nameit/simple-vdom)