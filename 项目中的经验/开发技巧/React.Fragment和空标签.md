# <> 与 React.Fragment

当我们需要在组件中返回多个元素时，我们通常会使用一个父元素包裹这些元素，但是这样会增加额外的 DOM 节点，如果我们不想要这些额外的 DOM 节点，我们可以使用 `React.Fragment` 或者 `<>` 来包裹这些元素。

在不需要 className、style、key 等属性的情况下，我们可以使用 `<>` 来包裹元素，如果需要这些属性，我们可以使用 `React.Fragment`。

```jsx
import React from "react";

function App() {
  return (
    <>
      <h1>React.Fragment</h1>
      <p>React.Fragment 可以用来包裹多个元素</p>
    </>
  );
}

export default App;
```

```jsx
import React from "react";

function App() {
  return (
    <React.Fragment className="test">
      <h1>React.Fragment</h1>
      <p>React.Fragment 可以用来包裹多个元素</p>
    </React.Fragment>
  );
}

export default App;
```
