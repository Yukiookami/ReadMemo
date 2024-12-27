# 原生的自定义事件与监听 CustomEvent

CustomEvent 是一种 JavaScript 构造函数，用于创建自定义事件。它允许开发者定义自己的事件类型，并传递自定义的数据。这种事件可以通过 dispatchEvent 方法触发，并用 addEventListener 监听。

## 创建自定义事件

要创建自定义事件，可以使用 CustomEvent 构造函数。它接受两个参数：事件类型和一个包含事件信息的对象。

```js
// 创建一个自定义事件
const event = new CustomEvent("my-event", {
  detail: {
    message: "Hello, world!",
  }, // 事件信息
  bubbles: true, // 是否允许事件冒泡，默认值为 false。
  cancelable: true, // 是否允许阻止默认行为，默认值为 false。
});

// 触发自定义事件
document.dispatchEvent(event);
```

## 监听自定义事件

要监听自定义事件，可以使用 addEventListener 方法。它接受三个参数：事件类型、事件处理函数和一个可选的配置对象。

```js
// 监听自定义事件
document.addEventListener("my-event", (event) => {
  console.log("Received event:", event.detail.message);
});
```

## 示例

在 axios 的二次封装中，我们无法访问到 redux,context,vuex,paint 等全局变量，但是我们可以通过自定义事件来实现，来做到在请求完成后，通知某个特定的组件。

例如，我们可以在请求开始以及结束的时候，打开遮罩。

```js
const openMask = (url: string) => {
  const lastPart = url.match(/\/[^/]*$/)?.[0] ?? "";
  if (dontOpenMask.includes(lastPart)) return;
  const event = new CustomEvent("apiRequestStart");
  window.dispatchEvent(event); // APIマスクを表示するイベントをディスパッチ
};

const closeMask = (url: string) => {
  const lastPart = url.match(/\/[^/]*$/)?.[0] ?? "";
  if (dontOpenMask.includes(lastPart)) return;
  const event = new CustomEvent("apiRequestEnd");
  window.dispatchEvent(event); // APIマスクを非表示にするイベントをディスパッチ
};
```

在发出请求的时候调用 openMask，请求结束的时候调用 closeMask。

```js
axiosInstance.interceptors.request.use((config: any) => {
  // 在这里调用 openMask
  openMask(config.url);
  config.headers["Content-Type"] = "application/json";

  // トークン
  if (Object.values(httpAPIFromInternet).includes(config.url)) {
    return Promise.resolve(config);
  }

  const token = JSON.parse(
    sessionStorage.getItem("token") || "{}"
  )?.accessToken;
  token && (config.headers.Authorization = `Bearer ${token}`);

  return Promise.resolve(config);
});

// レスポンスインターセプター（レスポンスを受信した後のインターセプター）
axiosInstance.interceptors.response.use(
  async (response: AxiosResponse) => {
    // 在请求结束的时候调用 closeMask
    closeMask(response.config.url ?? "");

    return response;
  },
  async (error: any) => {
    const { response, code } = error;
    const status = response?.status;
    closeMask(response.config.url ?? "");

    // 判断是否是网络错误
    const codeError = [
      "ECONNABORTED", // 超时
      "ENOTFOUND", // 找不到服务器
      "ECONNREFUSED", // 拒绝连接
      "ECONNRESET", // 连接被重置
    ];
    if (codeError.includes(code)) {
      redirectTo(url404);
      return Promise.reject(error);
    }
  }
);
```
