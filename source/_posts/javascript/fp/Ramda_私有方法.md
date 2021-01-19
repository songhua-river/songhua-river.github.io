---
title: Ramda 私有方法源码分析
mathjax: true
date: 2021-01-16 16:26:50
categories:
  - JavaScript
  - 函数式编程
tags:
  - 函数式编程
  - Ramda
abbrlink:
---

#### _objectIs

[Object.is](https://developer.mozilla.org/zh-cn/docs/web/javascript/reference/global_objects/object/is) 用于判断两值是否项等

```javascript
function _objectIs(a, b) {
  // MDN polyfill
  if (a === b) {
    // Steps 6.b-6.e: +0 != -0
    return a !== 0 || 1 / a === 1 / b;
  } else {
    // Step 6.a: NaN == NaN
    return a !== a && b !== b;
  }
}
export default typeof Object.is === 'function' ? Object.is : _objectIs;

```

#### _arrayFromIterator

```javascript
export default function _arrayFromIterator(iter) {
  var list = [];
  var next;
  while (!(next = iter.next()).done) {
    list.push(next.value);
  }
  return list;
}
```

#### _functionName

[function.name](https://developer.mozilla.org/zh-cn/docs/web/javascript/reference/global_objects/function/name) 可能被压缩工具修改，不能依赖函数名来判断函数

作者通过正则方式判断

```javascript
export default function _functionName(f) {
  // String(x => x) evaluates to "x => x", so the pattern may not match.
  var match = String(f).match(/^function (\w*)/);
  return match == null ? '' : match[1];
}

```

#### _has

判断属性在对象的实例上，而不是在原型上

```javascript
export default function _has(prop, obj) {
  return Object.prototype.hasOwnProperty.call(obj, prop);
}
```

等价于

```javascript
Reflect.has(obj, prop)
```


#### _isArguments

实现了 `callee` 方法数组也可以当作是形式参数

```javascript
var toString = Object.prototype.toString;
var _isArguments = (function() {
  return toString.call(arguments) === '[object Arguments]' ?
    function _isArguments(x) { return toString.call(x) === '[object Arguments]'; } :
    function _isArguments(x) { return _has('callee', x); };
}());
```

#### _concat 

通过双循环，涵盖了array-like类型，

```javascript
/**
 * Private `concat` function to merge two array-like objects.
 *
 * @private
 * @param {Array|Arguments} [set1=[]] An array-like object.
 * @param {Array|Arguments} [set2=[]] An array-like object.
 * @return {Array} A new, merged array.
 * @example
 *
 *      _concat([4, 5, 6], [1, 2, 3]); //=> [4, 5, 6, 1, 2, 3]
 */
export default function _concat(set1, set2) {
  set1 = set1 || [];
  set2 = set2 || [];
  var idx;
  var len1 = set1.length;
  var len2 = set2.length;
  var result = [];

  idx = 0;
  while (idx < len1) {
    result[result.length] = set1[idx];
    idx += 1;
  }
  idx = 0;
  while (idx < len2) {
    result[result.length] = set2[idx];
    idx += 1;
  }
  return result;
}

```


#### _dispatchable

dispatchable翻译为调度单元，这个方法用在了类似 [`all`]()

```javascript
export default function _dispatchable(methodNames, transducerCreator, fn) {
  return function() {
    if (arguments.length === 0) {
      return fn();
    }
    var obj = arguments[arguments.length - 1];
    if (!_isArray(obj)) {
      var idx = 0;
      while (idx < methodNames.length) {
        if (typeof obj[methodNames[idx]] === 'function') {
          return obj[methodNames[idx]].apply(obj, Array.prototype.slice.call(arguments, 0, -1));
        }
        idx += 1;
      }
      if (_isTransformer(obj)) {
        var transducer = transducerCreator.apply(null, Array.prototype.slice.call(arguments, 0, -1));
        return transducer(obj);
      }
    }
    return fn.apply(this, arguments);
  };
}
```