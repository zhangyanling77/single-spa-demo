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
* 创建router.js
```javascript
import React, { Suspense, lazy, memo } from 'react';
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

const Layouts = lazy(() => import('./components/layouts'));
const Welcome = lazy(() => import('./components/welcome'));

const RouterComponent = () => {
  return (
    <Router>
      <Suspense fallback={<div>loading...</div>}>
        <Layouts>
          <Switch>
            <Route exact={true} path='/' component={Welcome} />
            <Route render={() =><h1>404 Not Found</h1>} />
          </Switch>
        </Layouts>
      </Suspense>
    </Router>
  )
};

export default memo(RouterComponent);
```
* components/layouts.js
```javascript
import React from 'react';
import { Link } from 'react-router-dom';

export default ({ children }) => {
  return (
    <div style={{padding: 40}}>
      <div>
        <Link to='/react'>react app</Link> | <Link to='/vue'>vue app</Link>
      </div>
      {children}
    </div>
  )
}
```
* components/welcome.js
```javascript
import React from 'react';

export default () => {
  return (
    <h1>Welcome to My-Single-Spa App ！</h1>
  )
}
```
* 主应用安装qiankun并注册子应用
```bash
yarn add qiankun
```
* single-spa-config.js

```javascript
import { registerMicroApps, runAfterFirstMounted, setDefaultMountApp, start } from 'qiankun';
import './index.css';

import renderReact from './render/reactRender';
import renderVue from './render/vueRender';
// 处于激活的路由
function genActiveRule(routerPrefix) {
  return location => location.pathname.startsWith(routerPrefix);
}

/**
 * step1 初始化应用
 */
renderReact({ appContent: '', loading: true });

/**
 * step2 注册子应用
 */
registerMicroApps([
  { 
    name: 'react-child-demo', 
    entry: '//localhost:3001', 
    renderReact, 
    activeRule: genActiveRule('/react') 
  },
  { 
    name: 'vue-child-demo', 
    entry: { 
      scripts: ['//localhost:8080']
    }, 
    renderVue, 
    activeRule: genActiveRule('/vue') 
  },
], {
  beforeLoad: [
    app => {
      console.log('[lifeSyscle] before laod %c%s', 'color: green;', app.name)
    },
  ],
  beforeMount: [
    app => {
      console.log('[LifeCycle] before mount %c%s', 'color: red;', app.name);
    },
  ],
  afterUnmount: [
    app => {
      console.log('[LifeCycle] after unmount %c%s', 'color: blue;', app.name);
    },
  ],
});

/**
 * step3 设置默认进入的子应用
 */
setDefaultMountApp('/react');

/**
 * step4 启动应用
 */
start({ prefetch: true, jsSandbox: true, singular: true, fetch: window.fetch });

runAfterFirstMounted(() => {
  console.log('[MainApp] first app mounted');
});

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
* 2、创建 vue 子应用
```bash
vue create vue-child-demo && cd vue-child-demo
yarn serve
```
运行在http://localhost:8080/
