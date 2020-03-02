## single-spa-demo
学习single-spa的一个demo
* 技术栈
single-spa/qiankun、react、vue等及其生态

## 创建项目

### 1、创建主应用 
主应用可以是任意技术栈，这里以react为例：

```bash
npx create-react-app single-spa-demo && cd single-spa-demo
yarn start
```
运行在http://localhost:3000

* 安装路由库
```bash
yarn add react-router-dom
```
* 创建路由配置文件 router.config.js
```javascript
import Home from './home'
import About from './about'

export default {
  routes: [
    {
      path: '/home',
      component: Home,
    },
    {
      path: '/about',
      component: About,
    },
  ],

}
```
* App.js
```javascript
import React from 'react';
import { BrowserRouter as Router, Switch, Route } from 'react-router-dom';
import Layout from './layout'
import routeConfig from './router.config'

function App() {
  return (
    <div>
      <Router>
        <Layout>
          <Switch>
            {
              routeConfig.routes.map(item => (
                <Route key={item.path} path={item.path} component={item.component} />
              ))
            }
          </Switch>
        </Layout>
      </Router>
    </div>
  );
}

export default App;

```
* layout.js
```javascript
import React from 'react';
import { Link } from 'react-router-dom';

export default ({ children }) => {
  return (
    <div style={{padding: 40}}>
      <div>
        <Link to='/home'>home</Link> | <Link to='/about'>about</Link>
      </div>
      {children}
    </div>
  )
}
```
* home.js
```javascript
import React from 'react';

export default () => (
  <div>Home</div>
)
```
* about.js
```javascript
import React from 'react';

export default () => (
  <div>About</div>
)
```
* 主应用安装qiankun并注册子应用
```bash
yarn add qiankun
```
* single-spa-config.js

```javascript
import { registerMicroApps, start } from 'qiankun';

function render({ appContent, loading }) {
  const container = document.getElementById('root');
  ReactDOM.render(<Framework loading={loading} content={appContent} />, root);
}

function genActiveRule(routerPrefix) {
  return location => location.pathname.startsWith(routerPrefix);
}

registerMicroApps([
  { 
    name: 'react-child-demo', 
    entry: '//localhost:3001', 
    render, 
    activeRule: genActiveRule('/react') 
  },
  { 
    name: 'vue-child-demo', 
    entry: { scripts: ['//localhost:8080/main.js'] }, 
    render, 
    activeRule: genActiveRule('/vue') 
  },
]);

start();
```
### 2、创建子应用
* 在主应用根目录下创建子应用文件夹：
```bash
mkdir sub-app && cd sub-app
```
#### 创建两个子应用，以react、vue为例 （angular不做研究）
* 1、创建 react 子应用
```bash
npx create-react-app react-child-demo && cd react-child-demo
yarn start
```
运行在http://localhost:3001

* 2、创建 vue 子应用
```bash
vue create vue-child-demo && cd vue-child-demo
yarn serve
```
运行在http://localhost:8080/
