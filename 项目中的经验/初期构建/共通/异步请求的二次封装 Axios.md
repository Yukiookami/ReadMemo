# axios

## axios 的封装

axios 的封装可以分为以下几个部分：发送请求、请求拦截、响应拦截、错误处理。

### 发送请求

封装 axios.create()方法，创建 axios 实例，设置默认配置，如 baseURL、timeout 等。

```javascript
import axios from "axios";

const instance = axios.create({
  baseURL: "http://localhost:3000",
  timeout: 1000,
  headers: {
    "Content-Type": "application/json",
  },
});

export default instance;
```

### 请求拦截 instance.interceptors.request

在请求发送前，可以对请求进行拦截，如添加 token、设置请求头等。

在 create 之后，使用 interceptors.request.use()方法，对请求进行拦截。

```javascript
import axios from "axios";

const instance = axios.create({
  baseURL: "http://localhost:3000",
  timeout: 1000,
  headers: {
    "Content-Type": "application/json",
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

在 create 之后，使用 interceptors.response.use()方法，对响应进行拦截。

↓ 这个部分

```javascript
  (response) => {
    // 对响应数据做点什么
    return response;
  },
```

```javascript
import axios from "axios";

const instance = axios.create({
  baseURL: "http://localhost:3000",
  timeout: 1000,
  headers: {
    "Content-Type": "application/json",
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

↓ 这个部分

```javascript
(error) => {
  // 对响应错误做点什么
  return Promise.reject(error);
};
```

```javascript
import axios from "axios";

const instance = axios.create({
  baseURL: "http://localhost:3000",
  timeout: 1000,
  headers: {
    "Content-Type": "application/json",
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

在错误请求处理中，可以单独针对网络错误、超时错误、业务错误等进行处理。

```javascript
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

### 完整示例（包含 API 调用时的遮罩）

```javascript
import axios, {
  AxiosInstance,
  AxiosRequestConfig,
  AxiosResponse,
  AxiosResponseHeaders,
  RawAxiosResponseHeaders,
} from "axios";
import { axiosSettingObj, httpAPIFromInternet } from "./status";
import { ApiUrl } from "../assets/data/TkApiUrlProperty";

export interface IResponse {
  code: number | string;
  data: any;
  msg: string;
}

export interface httpResponse<T = any, D = any> {
  data: T;
  status: number;
  statusText: string;
  headers: RawAxiosResponseHeaders | AxiosResponseHeaders;
  config: AxiosRequestConfig<D>;
  request?: any;
}

let axiosInstance: AxiosInstance = axios.create(axiosSettingObj);
const url404 = `${process.env.REACT_APP_BASE_NAME ?? ""}/error`;
const url401 = `${process.env.REACT_APP_BASE_NAME ?? ""}/error401`;

let isRedirecting = false; // リダイレクト中かどうかを管理するフラグ
const REDIRECT_DELAY = 100; // setTimeoutの延期を管理する構造体

const redirectTo = (url: string) => {
  if (!isRedirecting) {
    isRedirecting = true;
    setTimeout(() => {
      window.location.href = url;
    }, REDIRECT_DELAY);
  }
};

// Maskを開けないAPI
const dontOpenMask = [ApiUrl.API_COM005, ApiUrl.API_COM009, ApiUrl.API_COM004];

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

// リクエストインターセプター（リクエストを送信する前のインターセプター）
axiosInstance.interceptors.request.use((config: any) => {
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

/**
 * エラーが発生した場合にトークンのリフレッシュを処理します。
 *
 * @param error - エラーオブジェクトです。
 * @returns 更新されたレスポンスまたはエラーで拒否される Promise です。
 */
const handleTokenRefresh = async (error: any) => {
  // sessionStorageからトークンを取得する
  const token = sessionStorage.getItem("token");

  // トークンが存在する場合、リフレッシュトークンを取得する
  if (token) {
    const refreshToken = JSON.parse(token).refreshToken;

    // リフレッシュトークンが存在する場合、アクセストークンをリフレッシュするリクエストを送信する
    if (refreshToken) {
      try {
        const res = await axiosInstance.post(ApiUrl.API_COM004, {
          refreshToken,
        });

        if (res.data.status && res.data.status !== 200) {
          // リフレッシュトークンのリフレッシュに失敗した場合、トークンをクリアしてログインページにリダイレクトする
          sessionStorage.clear();
          redirectTo(url401);
        }

        // 新しいアクセストークンを保存する
        sessionStorage.setItem("token", JSON.stringify(res.data));

        // 失敗したリクエストを新しいアクセストークンで再送信する
        error.config.headers["Authorization"] =
          "Bearer " + res.data.accessToken;

        // 新しいレスポンスを返す
        return axiosInstance.request(error.config);
      } catch (error) {
        // リフレッシュトークンのリフレッシュに失敗した場合、トークンをクリアしてログインページにリダイレクトする
        sessionStorage.clear();
        redirectTo(url404);
      }
    } else {
      // リフレッシュトークンが存在しない場合、トップページにリダイレクトする
      redirectTo(url404);
    }
  } else {
    // トークンが存在しない場合、ログインページにリダイレクトする
    redirectTo(url404);
  }

  // デフォルトのエラーレスポンスを返す
  return Promise.reject(error);
};

// レスポンスインターセプター（レスポンスを受信した後のインターセプター）
axiosInstance.interceptors.response.use(
  async (response: AxiosResponse) => {
    closeMask(response.config.url ?? "");
    // ここでバックエンドから返されたデータを解構造化して返し、後続の取得を容易にします
    if (
      response.data.status === 401 &&
      response.config.url !== ApiUrl.API_COM004
    ) {
      return await handleTokenRefresh(response).catch((err) => {
        sessionStorage.clear();
        redirectTo(url401);
        return Promise.reject(err);
      });
    }
    return response;
  },
  async (error: any) => {
    const { response, code } = error;
    const status = response?.status;
    closeMask(response.config.url ?? "");

    const codeError = [
      "ECONNABORTED",
      "ENOTFOUND",
      "ECONNREFUSED",
      "ECONNRESET",
    ];
    if (codeError.includes(code)) {
      redirectTo(url404);
      return Promise.reject(error);
    }

    if (status === 401 && response.config.url !== ApiUrl.API_COM004) {
      return await handleTokenRefresh(error).catch((err) => {
        sessionStorage.clear();
        redirectTo(url401);
        return Promise.reject(err);
      });
    } else if (status === 403) {
      // 403の場合はリダイレクトを行わず、エラーメッセージをそのまま返す
      return error.response;
    } else {
      redirectTo(url404);
      return Promise.reject(error);
    }
  }
);

export default axiosInstance;
```

### 精简版 axios 封装

```typescript
import axios, {
  AxiosError,
  AxiosInstance,
  AxiosResponse,
  InternalAxiosRequestConfig,
} from "axios";

const axiosInstance: AxiosInstance = axios.create({
  // API URL，一般存储到环境变量中
  baseURL: "/",
});

axiosInstance.interceptors.request.use((config: InternalAxiosRequestConfig) => {
  // 请求头数据格式 一般来说都是application/json 特殊情况下可能有form-data
  config.headers["Content-Type"] = "application/json";

  // 获取token和设置token相关处理
  const token = "token";
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }

  return Promise.resolve(config);
});

axiosInstance.interceptors.response.use(
  // API返回成功，状态码为200
  (response: AxiosResponse) => {
    // 对响应数据的处理（当请求返回成功为200，但业务代码需要特殊处理的时候，写在这里）

    // 返回响应数据
    return response;
  },
  // API返回失败，状态码不为200
  (error: AxiosError) => {
    const { code } = error;
    // 对响应错误的处理（当请求返回失败，需要特殊处理的时候，写在这里）
    // 例如：请求超时、网络断开、404、500、401、403、304等

    // 特殊错误码处理
    const codeError = [
      // 请求超时
      "ECONNABORTED",
      // 无法解析主机
      "ENOTFOUND",
      // 连接被拒绝
      "ECONNREFUSED",
      // 连接被重置
      "ECONNRESET",
      // 网络错误
      "ERR_NETWORK",
    ];
    if (code && codeError.includes(code)) {
      // redirectTo(url404);
      return Promise.reject(error);
    }

    return Promise.reject(error);
  }
);
```
