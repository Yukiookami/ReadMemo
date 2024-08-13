# axios

## axios 的封装

axios的封装可以分为以下几个部分：发送请求、请求拦截、响应拦截、错误处理。

### 发送请求

封装axios.create()方法，创建axios实例，设置默认配置，如baseURL、timeout等。

```javascript
import axios from 'axios';

const instance = axios.create({
  baseURL: 'http://localhost:3000',
  timeout: 1000,
  headers: {
    'Content-Type': 'application/json',
  },
});

export default instance;
```

### 请求拦截 instance.interceptors.request

在请求发送前，可以对请求进行拦截，如添加token、设置请求头等。

在create之后，使用interceptors.request.use()方法，对请求进行拦截。

```javascript
import axios from 'axios';

const instance = axios.create({
  baseURL: 'http://localhost:3000',
  timeout: 1000,
  headers: {
    'Content-Type': 'application/json',
  },
});

instance.interceptors.request.use(
  (config) => {
    // 在发送请求之前做些什么
    return config;
  },
  (error) => {
    // 对请求错误做些什么
    return Promise.reject(error);
  }
);

export default instance;
```

### 响应拦截 interceptors.response.use()

在接收到响应数据时，可以对响应进行拦截，如对响应数据进行处理、错误处理等。

在create之后，使用interceptors.response.use()方法，对响应进行拦截。

↓这个部分

```javascript
  (response) => {
    // 对响应数据做点什么
    return response;
  },
```

```javascript

import axios from 'axios';

const instance = axios.create({
  baseURL: 'http://localhost:3000',
  timeout: 1000,
  headers: {
    'Content-Type': 'application/json',
  },
});

instance.interceptors.response.use(
  (response) => {
    // 对响应数据做点什么
    return response;
  },
  (error) => {
    // 对响应错误做点什么
    return Promise.reject(error);
  }
);

export default instance;
```

### 错误处理 interceptors.response.use()

在响应拦截中，可以对错误进行处理，如错误提示、错误码处理等。

↓这个部分

```javascript
 (error) => {
    // 对响应错误做点什么
    return Promise.reject(error);
  }
```

```javascript
import axios from 'axios';

const instance = axios.create({
  baseURL: 'http://localhost:3000',
  timeout: 1000,
  headers: {
    'Content-Type': 'application/json',
  },
});

instance.interceptors.response.use(
  (response) => {
    // 对响应数据做点什么
    return response;
  },
  (error) => {
    // 对响应错误做点什么
    return Promise.reject(error);
  }
);

export default instance;
```
