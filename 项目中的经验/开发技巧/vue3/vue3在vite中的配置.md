# vite 的配置文件

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

## 配置文件属性说明

- `plugins`：插件配置
- `publicPath`：公共路径
- `base`：基础路径
- `build`：构建配置
  - `outDir`：输出目录
  - `assetsDir`：静态资源目录
- `css`：CSS 配置
  - `preprocessorOptions`：预处理器配置
    - `scss`：SCSS 配置
      - `additionalData`：额外数据，用于引入 css 全局变量
