# token 的无感刷新

## 现象

在开发中，我们经常会遇到这样的场景，用户在使用我们的应用时，我们需要获取用户的 token，但是 token 有时效性，过期后需要重新获取，这时候我们就需要在 token 过期时，重新获取 token，这个过程就是 token 的刷新。

## 解决方案

### 1. 无感刷新

无感刷新是指在用户操作时，后台自动刷新 token，用户无感知。这种方式可以保证用户的操作不会因为 token 过期而中断。

### 2. 实现方式

在 axios 中，配置拦截器，当 token 过期时，重新获取 token。如果 refresh token 也过期了，就跳转到相应页面。

以 axios 为例：

```javascript
import axios from "axios";

// 创建 axios 实例
const instance = axios.create({
  baseURL: "http://localhost:3000",
  timeout: 1000,
  headers: {
    "Content-Type": "application/json",
  },
});

// 添加请求拦截器
instance.interceptors.request.use(
  (config) => {
    // 在发送请求之前做些什么
    // 获取 并配置token
    const token = localStorage.getItem("token");
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => {
    return Promise.reject(error);
  }
);

// 添加响应拦截器
instance.interceptors.response.use(
  (response) => {
    return response;
  },
  async (error) => {
    const originalRequest = error.config;
    if (error.response.status === 401 && !originalRequest._retry) {
      originalRequest._retry = true;
      const refreshToken = localStorage.getItem("refreshToken");
      if (refreshToken) {
        try {
          const res = await axios.post("/refreshToken", {
            refreshToken,
          });
          localStorage.setItem("token", res.data.token);
          return instance(originalRequest);
        } catch (error) {
          // refresh token 过期
          localStorage.removeItem("token");
          localStorage.removeItem("refreshToken");
          window.location.href = "/login";
        }
      } else {
        // refresh token 不存在
        localStorage.removeItem("token");
        localStorage.removeItem("refreshToken");
        window.location.href = "/login";
      }
    }
    return Promise.reject(error);
  }
);

export default instance;
```
