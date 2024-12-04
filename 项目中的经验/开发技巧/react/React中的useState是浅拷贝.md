# React 中的 useState 为浅拷贝

在 React 中，当我们需要声明一个会影响页面渲染的状态的时候，我们通常会使用`useState`来声明这个状态，`useState`返回一个数组，数组的第一个元素是当前状态的值，第二个元素是一个函数，用来更新状态的值。

```jsx
import React, { useState } from "react";

function App() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>+1</button>
    </div>
  );
}

export default App;
```

这个时候一定要小心，如果 useState 引用的状态是引用类型的话，会导致源数据被污染。
