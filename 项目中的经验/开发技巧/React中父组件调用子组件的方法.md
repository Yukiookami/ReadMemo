# 通过 forwardRef，ref，useImperativeHandle 的联动，实现父组件调用子组件方法

将子组件的方法暴露给父组件，可以通过 forwardRef，ref，useImperativeHandle 的联动来实现。

在子组件中

```jsx
import React, { forwardRef, useImperativeHandle } from "react";

const Child = forwardRef((props, ref) => {
  // 把子组件的ref暴露出去，并且可以调用暴露出去的子组件的方法
  useImperativeHandle(ref, () => ({
    childMethod() {
      console.log("childMethod");
    },
  }));

  return <div>Child</div>;
});
```

在父组件中

```jsx
import React, { useRef } from "react";

const Parent = () => {
  const childRef = useRef(null);

  const handleClick = () => {
    // 调用子组件暴露的方法
    childRef.current.childMethod();
  };

  return (
    <div>
      <Child ref={childRef} />
      <button onClick={handleClick}>调用子组件方法</button>
    </div>
  );
};
```

## forwardRef

forwardRef 是 React 中的一个高级 API，用于在函数组件中转发 ref。它允许父组件将 ref 传递给子组件中的 DOM 元素或类组件，从而使父组件能够直接访问子组件的引用。

### 使用场景

1. 访问子组件的 DOM 元素：当你需要在父组件中访问子组件的 DOM 元素时，可以使用 forwardRef。
2. 与第三方库集成：某些第三方库需要直接访问 DOM 元素，使用 forwardRef 可以方便地将 ref 传递给这些库。

## useImperativeHandle

useImperativeHandle 是一个自定义 Hook，用于在函数组件中暴露自定义的实例值或方法给父组件。它可以让你在函数组件中定义一个 ref，并通过 useImperativeHandle 将 ref 中的值暴露给父组件。
