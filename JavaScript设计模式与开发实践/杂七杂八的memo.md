# 等待后期汇总

js的设计模式和其它语言的设计模式有不小的区别，因为js是一个动态类型语言，弱类型让它天然实现了不少设计模式。

抽象工厂模式在js中的应用很少，基本用简单工厂模式就可以实现绝大多数需求。

工厂模式是我们给函数发指令他给我们结果就可以，不关心过程。而相对的，建造者模式是我们给函数发出请求让它一步一步去构建出我们想要的对象，主要关注的点就在过程。

---
和装饰器模式有关，使用apply是因为不确定有多少参数会传入，并且需要函数立即执行。

call():

call 方法调用一个函数，其第一个参数将被用作该函数内的 this 对象，其余参数将作为函数的参数传递。
语法示例：func.call(thisArg, arg1, arg2, ...)
示例：test.call(this, 'arg1', 'arg2')

apply():

apply 方法的作用与 call 类似，区别在于 apply 接受两个参数，第一个参数同样是作为函数内的 this 对象，第二个参数是一个数组或类数组对象，其中的数组元素将作为函数的参数传递。
语法示例：func.apply(thisArg, [argsArray])
示例：test.apply(this, ['arg1', 'arg2'])

bind():

bind 方法创建一个新的函数，在 bind 被调用时，这个新函数的 this 被指定为 bind 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。
bind 不会立即执行该函数，而是返回一个改变了上下文 this 后的新函数。
语法示例：func.bind(thisArg[, arg1[, arg2[, ...]]])
示例：const newTest = test.bind(this, 'arg1', 'arg2')
总结:

call 和 apply 都是立即调用函数，不同的是参数传递方式：call 需要将参数按顺序传递，而 apply 则是把参数放在数组中传递。
bind 用于创建新的函数，可以预设 this 值和初始参数，调用时再传入其他参数，但不会立即执行函数。

---

策略模式可以替代绝大部分if else，switch case，可以让代码更加清晰，易于维护。（当策略和函数传参相关时记得用箭头函数返回这个函数，不然会导致在定义时就直接执行）

```javascript
const func = (arr = "") => {
  console.log(arr)
}

// 会立即执行
const a = {
  'A': func(arr),
  'B': func(arr),
}

// 不会立即执行
const b = {
  'A': () => func(arr),
  'B': () => func(arr),
}

// 同样不会立即执行, 但是没有传参
const c = {
  'A': func,
  'B': func,
}
```

---
