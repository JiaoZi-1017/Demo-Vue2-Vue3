# 项目搭建

局部安装vue-cli 2.9.6和项目创建：

```cmd
# 1.初始化项目依赖文件
npm init -y
# 2.安装脚手架
cnpm i -D vue-cli@2.9.6
# 查看vue-cli版本号
npx vue -V
# 3.创建项目
npx vue init webpack project-one
```

![img](https://gitee.com/JiaoZi-1017/picgo/raw/master/images/202407141221260.png)

![img](https://gitee.com/JiaoZi-1017/picgo/raw/master/images/202407141221262.png)

# 项目配置

## 自动打开项目

```js
// vue.config.js
module.exports = {
    devServer: {
        open: true
      	// host: 'localhost'
    }
}
```

## 使用ElementUI

[官网](https://element.eleme.cn/#/zh-CN)

> 全局使用

安装：

````bash
npm i element-ui -S
````

在main.js中完整引入：

````js
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';

Vue.use(ElementUI);
````

示例：

````vue
<el-button>demo</el-button>
````

> 按需使用

安装：

````bash
npm i element-ui -S
````

````bash
npm i babel-plugin-component -D
````

在.babel.config.js新增以下内容：

````js
"plugins": [
    [
        "component",
        {
            "libraryName": "element-ui",
            "styleLibraryName": "theme-chalk"
        }
    ]
]
````

在main.js中引入：

````js
import { Button, Tag } from 'element-ui'

Vue.use(Button)
Vue.use(Tag)
````

或在项目目录下新建plugins文件夹，新建element.js文件

````js
import Vue from 'vue'
import { Button, Tag } from 'element-ui'

Vue.use(Button)
Vue.use(Tag)
````

````js
//main.js
import '../plugins/element.js'
````

示例：

````vue
<el-button>demo</el-button>
<el-tag>标签一</el-tag>
````

## 使用CSS预处理器Scss

[官网](https://www.sass.hk/)

安装：

````bash
cnpm i sass-loader@7 node-sass@4 -S
````

使用：

````vue
<style lang="scss"></style>
````

## 使用CSS预处理器Less

[官网](https://www.sass.hk/)

安装：

````bash
cnpm i less@3 less-loader@7 -S
````

使用：

````vue
<style lang="less"></style>
````

## 使用reset.css

任何一个项目都需要做样式重置，这里使用官方的样式重置内容，如果需要补充可手动添加

[官网](https://meyerweb.com/eric/tools/css/reset/)

将代码复制放在项目中新建的reset.css文件，然后在app.vue的style中进行引入使用

````scss
@import url('./assets/css/reset.css');
````

## 使用font-awesome图标库

[官网](https://fontawesome.dashgame.com/)

安装：

````bash
cnpm i font-awesome -D
````

在main.js中引入：

````js
import 'font-awesome/css/font-awesome.min.css'
````

使用：

````vue
<i class='fa fa-users'></i>
````

## 使用axios

[官网](http://www.axios-js.com/)

安装：

````bash
cnpm i -S axios
````

在main.js中引入：

````js
import axios from 'axios'

Vue.prototype.axios = axios// 挂载到原型，可在全局使用
````

axios二次封装：

1. 自定义js文件，对axios进行二次封装

   ````js
   import axios from 'axios'
   
   const service = axios.create({
       // baseURL会自动加在接口地址上
       baseURL: "/api",
       timeout: 3000 // 指定请求的超时毫秒数，如果请求超过这个时间，那么请求就会中断。
   })
   
   // 添加请求拦截器
   service.interceptors.request.use((config) => {
       // 在发送请求前做些什么
       // 获取并设置token
       // console.log(getToken('token'))
       // config.headers['token'] = getToken('token')
       return config
   },(error) => {
       // 对请求错误做些什么
       return Promise.reject(error)
   })
   
   // 添加响应拦截器
   service.interceptors.response.use((response) => {
       // 对响应数据做些什么
       console.log(response)
       let { status, message } = response.data
       if (status !== 200) {
           Message({message: message || 'error', type: 'warning'})
       }
       return response
   }, (error) => {
       // 对响应错误做点什么
       return Promise.reject(error)
   })
   
   export default service
   ````

2. 在main.js中引入：

   ````js
   import service from './service'
   
   Vue.prototype.service = service// 挂载到原型，可在全局使用
   ````

## 配置路由

安装vue-router：

````bash
cnpm i vue-router -S
````

配置路由（新建router文件夹和index.js文件）：

````js
import Vue from 'vue'
import Router from 'vue-router'
import Home from '../components/Home.vue'

Vue.use(Router)

export default new Router({
    routes: [
        {
            path: '/',
            component: Home
        }
    ],
    mode: 'history'
})
````

在main.js中引入：

````js
import router from './router'

new Vue({
  router,
  render: h => h(App),
}).$mount('#app')
````

````vue
<!-- 在App.vue设置路由出口 -->
<router-view></router-view>
````

路由懒加载：

- 使用ES中的import（官方推荐）：`component: () => import('@/components/Login')`
- 使用Vue异步组件：`component: resolve => require(['@/components/Home'], resolve)`

## 跨域处理

在vue.config.js中设置代理

```js
module.exports = {
    devServer: {
        open: true,
        proxy: {
            '/api': {
                target: 'http://localhost:3000/',
                changeOrigin: true,// 允许跨域
                pathRewrite: {
                    '^/api': ''
                }
            }
        }
    }
}
```

# vue2+ts项目重构

## 1.项目创建

![img](https://gitee.com/JiaoZi-1017/picgo/raw/master/images/202407141221263.png)

![img](https://gitee.com/JiaoZi-1017/picgo/raw/master/images/202407141221264.png)

## 2.安装第三方依赖（根据项目需求）

````bash
cnpm i element-ui font-awesome axios echarts@4 -S
````

## 3.在vue.config.js文件中添加ts文件匹配

````js
chainWebpack(chainableWebpack) {
  chainableWebpack.resovle = { extensions:['.js','.ts','.json','.d.ts']}
}
````

## 4.解决main.ts中的引入问题

在shims-vue.d.ts中加入 declare module 'echarts'

在shims-vue.d.ts中加入 declare module '*.js'

## 5.改写router文件夹中的js文件为ts文件，删除view文件夹

router文件夹中index.ts声明RouterConfig类型

````ts
import Vue from "vue"
import Router, { RouteConfig } from "vue-router"

const routes: Array<RouteConfig> = [] =  [
  {
    path: '/',
    redirect: '/login',
    meta:{
      hidden: true
    }
  },
  //  ...其它路由
]

Vue.use(Router)

const router = new Router({
  mode: "history",
  routes,
})

export default router
````

## 6.api文件夹

将api.js改写成api.ts，新建types.d.ts类型描述文件定义接口

声明IUserData接口类型：

````js
export interface IUserData {
  username: string;
  password: string;
}
````

## 7.login

引入types，对login进行约束：

````js
import {IUserData} from '../types'

// 登录接口
export function login(data:IUserData) {
    return service({
        method: 'post',
        url: '/login',
        data
    })
}
````

改写Login.vue文件：

````js
import { IUserData } from "@/types";
import Component from "vue-class-component";
import Vue from "vue";

@Component
export default class extends Vue {
  name = "Login";
  form: IUserData = {
    username: "",
    password: "",
  };
  rules = {
    username: [{ validator: nameRule, trigger: "blur" }],
    password: [{ validator: passRule, trigger: "blur" }],
  };

  login(form: any) {
    (this.$refs[form] as any).validate((valid: any) => {
      if (valid) {
        console.log("test1", this);

        login(this.form).then((res: any) => {
          // this.axios.post('http://localhost:3000/login' ,this.form).then((res) => {
          console.log(res);
          if (res.data.status === 200) {
            setToken("username", res.data.username);
            setToken("token", res.data.token);
            this.$message({ message: res.data.message, type: "success" });
            this.$router.push("/home");
          }
        });
      } else {
        console.log("test2", this);
        console.error(this.form);
      }
    });
  }
}
````

Login.vue文件报错：

- 使用@Component装饰器会报错，因为这是一个实验特性，需要在tsconfig.json文件中添加"experimentalDecorators": true

## 8.关闭eslint检测

在vue.config.js文件中添加如下配置：

````js
lintOnSave: false,
````

# vue3项目实战

## 1.项目创建

````bash
vue create project# 选择vue3项目
````

## 2.项目搭建

````bash
cnpm i element-plus axios less@3 less-loader@7 vue-router@4 font-awesome echarts@4  -S
````

## 3.项目配置

````js
// vue.config.js
module.exports = {
    devServer: {
        open: true // 自动打开浏览器
    }
}
````

````js
// router/index.js
// import { createRouter, createWebHashHistory } from 'vue-router'
import { createRouter, createWebHistory } from 'vue-router'
import Home from '../components/Home'

const routes = [
    {
        path: '/',
        name: 'Home',
        component: Home
    }
]
const router = createRouter({
    // history: createWebHashHistory(),
    history: createWebHistory(),
    routes
})

export default router
````

````js
// main.js
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'
import locale from 'element-plus/es/locale/lang/zh-cn'
import axios from 'axios'
import router from './router'

// createApp(App).use(ElementPlus, {local}).mount('#app')
const app = createApp(App)
app.config.globalProperties.$http = axios
app.use(router).use(ElementPlus, {locale}).mount('#app')
````

在App.vue页面给路由出口，在Home页面使用router-link to进行路由跳转

添加reset.css并在App.vue中引入：

````scss
@import url('./assets/reset.css');
html,body {
  width: 100%;
  height: 100%;
}
#app {
  width: 100%;
  height: 100%;
}
````