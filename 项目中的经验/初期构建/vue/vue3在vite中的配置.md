# vite 的配置文件

## vite.config.js 示例

```javascript
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()],
  publicPath: "./",
  base: "./", // 确保资源路径相对于当前目录
  build: {
    outDir: "dist", // 打包输出目录
    assetsDir: "assets", // 静态资源目录
  },
  css: {
    preprocessorOptions: {
      scss: {
        // 京东主题
        // 默认京东 APP 10.0主题 > @import "@nutui/nutui/dist/styles/variables.scss";
        // 京东科技主题 > @import "@nutui/nutui/dist/styles/variables-jdt.scss";
        // 京东B商城主题 > @import "@nutui/nutui/dist/styles/variables-jdb.scss";
        // 京东企业业务主题 > @import "@nutui/nutui/dist/styles/variables-jddkh.scss";
        // 全局变量
        additionalData: `@import "@nutui/nutui/dist/styles/variables-jdt.scss";
        @import "./src/assets/styles/variable.scss";`,
      },
    },
  },
});
```

## 完整配置文件属性说明

```javascript
// 导入必要的函数和插件
import { defineConfig } from "vite"; // Vite 的配置助手
import vue from "@vitejs/plugin-vue"; // Vue 插件用于 Vite
import path from "path"; // Node.js 的路径模块

export default defineConfig({
  // 项目根目录（默认是 process.cwd()）
  root: "./src",

  // 在开发或生产环境下的基础公共路径（默认是 '/'）
  base: "/my-app/",

  // 运行 Vite 时使用的模式（默认是 'development'，和production 的区别主要是会不会进行压缩等优化，以及环境变量的指定）
  mode: "development",

  // 定义可以在编译时替换的全局常量，可以通过常量名访问（默认是 {}） console.log(__APP_VERSION__); // 输出: "1.0.0"
  define: {
    __APP_VERSION__: JSON.stringify("1.0.0"), // 定义一个全局常量示例
  },

  // 使用的 Vite 插件数组
  plugins: [vue()], // 使用 Vue 插件

  // 模块解析相关配置，默认不用配置
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"), // 将 '@' 别名指向 './src' 目录
      components: path.resolve(__dirname, "./src/components"), // 将 'components' 别名指向 './src/components'
    },
    // 解析模块时考虑的文件扩展名（默认：['.mjs', '.js', '.ts', '.jsx', '.tsx', '.json']）
    extensions: [".js", ".jsx", ".ts", ".tsx", ".vue", ".json"],
  },

  // CSS 相关选项
  css: {
    // CSS 预处理器选项
    preprocessorOptions: {
      scss: {
        // 在每个 SCSS 文件中自动引入指定的 SCSS 文件
        additionalData: `@import "@/styles/variables.scss";`,
      },
    },
    // CSS 模块选项
    modules: {
      scopeBehaviour: "local", // 默认是 'local'，也可以设置为 'global'
      // 生成的 CSS 类名的命名模式（默认是 '[hash]'）
      generateScopedName: "[name]__[local]___[hash:base64:5]",
    },
  },

  // 开发服务器配置
  server: {
    port: 3000, // 使用的端口号（默认是 3000）
    open: true, // 服务器启动时是否自动在浏览器中打开应用（默认是 false）
    proxy: {
      // 开发期间用于代理 API 请求的配置
      "/api": {
        target: "https://api.example.com", // 代理目标服务器
        changeOrigin: true, // 将主机头的原点更改为目标 URL（默认是 false）
        // 重写 URL 路径，移除 '/api' 前缀
        rewrite: (path) => path.replace(/^\/api/, ""),
      },
    },
  },

  // 生产构建选项
  build: {
    outDir: "dist", // 构建输出目录（默认是 'dist'）
    sourcemap: true, // 是否生成 source map 以便调试（默认是 false）
    minify: "esbuild", // 使用的压缩工具（'esbuild', 'terser' 或 false）（默认是 'esbuild'）
    // Rollup 特定的选项
    rollupOptions: {
      input: {
        main: "./index.html", // 主应用的入口点
        nested: "./nested/index.html", // 额外的入口点（MPA应用的时候使用）
      },
      output: {
        // 入口块的文件命名模式（默认是 'assets/[name]-[hash].js'）
        entryFileNames: "assets/[name]-[hash].js",
        // 共享块的文件命名模式（默认是 'assets/[name]-[hash].js'）
        chunkFileNames: "assets/[name]-[hash].js",
        // 资源文件的命名模式（默认是 'assets/[name]-[hash][extname]'）
        assetFileNames: "assets/[name]-[hash][extname]",
      },
    },
    cssCodeSplit: true, // 是否启用 CSS 代码分割（默认是 true）
    assetsInlineLimit: 4096, // 以 base64 内联的资源的最大大小（以字节为单位，默认是 4096）
  },

  // 依赖优化选项
  optimizeDeps: {
    include: ["lodash"], // 显式包含需要优化的依赖
    exclude: ["vue"], // 显式排除不需要优化的依赖（默认排除对等依赖）
  },

  // 服务器端渲染（SSR）选项
  ssr: {
    noExternal: ["some-external-package"], // 在 SSR 构建中不将某些包视为外部依赖
  },

  // 作为纯静态资产提供服务的目录（默认是 'public'）
  publicDir: "static",

  // 额外要包含为静态资产的文件模式
  assetsInclude: ["**/*.svg", "**/*.png"],

  // 加载 .env 文件的目录（默认是项目根目录）
  envDir: "./env",

  // 暴露给客户端的环境变量的前缀（默认为VITE_）
  envPrefix: ["VITE_", "APP_"], // 仅以这些前缀开头的变量会被暴露

  // 用于转换代码的 esbuild 选项
  esbuild: {
    jsxFactory: "h", // JSX 工厂函数（默认是 React 项目的 'React.createElement'）
    jsxFragment: "Fragment", // JSX 片段函数（默认是 React 项目的 'React.Fragment'）
    minify: true, // 是否启用 esbuild 的压缩（默认是 false）
  },
});
```
