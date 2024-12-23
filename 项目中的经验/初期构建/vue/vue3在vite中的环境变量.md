# vue3 在 vite 中的环境变量

## 新建.env 文件

通过新建`.env`文件来定义环境变量，`.env`文件的内容如下：

```shell
VITE_APP_TITLE=Vite Vue3
VITE_APP_VERSION=1.0.0
```

针对不同的环境，可以新建`.env.development`、`.env.production`等文件来定义不同的环境变量。

## 在项目中使用

在项目中使用环境变量，只需要在项目中使用`import.meta.env`即可获取到环境变量，例如：

```javascript
console.log(import.meta.env.VITE_APP_TITLE); // Vite Vue3
console.log(import.meta.env.VITE_APP_VERSION); // 1.0.0
```

## .env 文件的命名规则

`.env`文件的命名规则如下：

- `.env`：默认的环境变量文件，所有环境都会加载
- `.env.local`：本地环境变量文件，会覆盖`.env`文件的配置
- `.env.[mode]`：指定模式的环墶变量文件，例如`.env.development`、`.env.production`等
- `.env.[mode].local`：指定模式的本地环墶变量文件，会覆盖`.env.[mode]`文件的配置

他们按照以上顺序依次覆盖。
