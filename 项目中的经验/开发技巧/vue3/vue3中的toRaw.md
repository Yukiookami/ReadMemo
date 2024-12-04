# vue3 中的 toRaw

## 1. 介绍

在 Vue3 中，我们经常会使用 `reactive` 来创建一个响应式对象，但是有时候我们需要获取这个对象的原始值，这个时候我们就可以使用 `toRaw` 来获取这个对象的原始值。

## 2. 使用

```javascript
import { reactive, toRaw } from "vue";

const obj = reactive({
  name: "张三",
  age: 18,
});

console.log(obj); // Proxy { name: '张三', age: 18 }

const rawObj = toRaw(obj);

console.log(rawObj); // { name: '张三', age: 18 }
```

有 Proxy 的存在的时候，会导致有些存储会出现一些问题，比如存储到 localStorage 中，或者 indexDB，值会变成 Proxy 对象，这个时候我们就可以使用`toRaw`来获取原始值。

## 3. 总结

`toRaw` 是一个非常实用的方法，可以用来获取一个响应式对象的原始值，避免 Proxy 对象的一些问题。

**不过还是要尽可能的避免直接去操作响应式数据，最好能够操作的数据和响应式数据分开，响应式数据只负责页面渲染，页面渲染之外的逻辑都交给普通数据来处理。**

如果还是需要直接操作响应式数据的时候，可以使用`toRaw`来获取原始值，并且深拷贝一份，避免直接操作响应式数据导致的问题。

**要注意的是，`toRaw`不是深拷贝，所以直接操作的时候依然会影响到响应式数据。**
