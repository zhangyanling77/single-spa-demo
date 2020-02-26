## single-spa-demo
学习single-spa的一个demo

## 创建项目

### 1、创建主应用 
主应用可以是任意技术栈，这里以react为例：

```bash
npx create-react-app single-spa-demo && cd single-spa-demo
```
* 安装路由库
```bash
yarn add react-router-dom
```
* 创建路由配置文件 router.config.js
```javascript
export default {
  routes: [
    {
      path: '/home',
      component: './home',
    },
    {
      path: '/about',
      component: './about'
    }
  ]

}
```
