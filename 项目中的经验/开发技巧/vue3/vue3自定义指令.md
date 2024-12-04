# vue3 自定义指令

## 1. 介绍

在 Vue3 中，我们可以通过自定义指令来扩展 Vue 的功能，自定义指令是一个函数，接收两个参数，第一个参数是元素，第二个参数是一个对象，对象中包含了一些属性，比如`binding`、`value`等。

## 2. 使用

首先新建一个文件，比如`directives.js`，在这个文件中定义一个自定义指令：

```javascript
export default {
  mounted(el, binding) {
    el.focus();
  },
};
```

然后在 main.js 中引入这个文件，并且使用`app.directive`来注册这个指令：

```javascript
import { createApp } from "vue";
import App from "./App.vue";
import directives from "./directives";

const app = createApp(App);

app.directive("focus", directives);

app.mount("#app");
```

然后就可以和普通的 vue 指令一样使用了

```vue
<template>
  <input v-focus />
</template>
```
