# 基于 dotenv 的环境变量配置

## 安装 dotenv

```bash
npm install dotenv
```

## 在根目录下创建脚本

例如创建一个`.env.production_combined_environment-Internet`文件，内容如下：

```shell
VITE_APP_TITLE=Vite Vue3
VITE_APP_VERSION=1.0.0
```

此时它并不能在打包的时候自动识别，因为这是我们自定义命名的环境变量。

因此我们需要配置 package.json 文件，添加如下内容：

```json

  "scripts": {
    "build:production_combined_environment-Internet": "dotenv -e .env.production_combined_environment-Internet react-scripts build",
  },
```

这样我们在打包的时候就可以使用`npm run  build:production_combined_environment-Internet`来打包了。
