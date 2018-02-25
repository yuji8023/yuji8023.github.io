---
layout: post
title: 大型PC项目Vue重构总结
date: 2018-02-06 23:00:00
categories: blog
tags: [js]
description: 关于云学院项目Vue重构的总结
---

## 前言

由于项目开发初期处于赶工状态，项目需求经常改动，参与开发的人员不固定及水平参差不齐，也没有一个基本的开发规范，导致整个项目的前端代码异常庞大及难以维护修改。在基础业务模块经过长时间的反复修改之后这个问题愈发突出。加上注释不明，导致不同成员之间彼此修改代码的成本很高，甚至修改不了的情况。痛定思痛之后，绝对用当前较火的Vue技术对整个前端项目进行重构。

由于这篇文章是我在重构过程中不断完善的，可能中间会有一些讨论形式，后期我也都尽量补上我的解决方案

## 项目介绍

+ 前端框架：Vue
+ 工具：Webpack、Node、Git、Npm
+ 语法：ES6
+ 技术栈：`Vue`、`Vue-Router`、`Vuex`、`Axios`、`tesla-ui`、`Sass`、`Echarts`、`Lodash`、`js-sha256`、`cos-js-sdk-v4` etc.

项目结构：

![项目结构图片](/img/blog/project-structure.png)

`index.js`是使用express搭建的一个后台服务，你可以将它看做一个[history模式](https://router.vuejs.org/zh-cn/essentials/history-mode.html),下面会讲到History模式需要注意的地方。
**history模式下的项目在构建后必须运行在服务器上或者一个 HTTP 服务上 才能正常运行。**
![http server tip](/img/blog/http-tip.png)

可以通过`Node + Express`搭建一个静态服务器
通过` node index` 命令可以启动一个node后台服务
通过3000端口访问

    localhost:3000

因为vue项目是完全前后端分离，所以，只需要一个静态资源服务器就可以。 在index.js里面

    app.use(exp.static('dist'))

这句话的作用是将dist文件夹指定为静态文件资源的root目录
[express文档](http://www.expressjs.com.cn/4x/api.html#express.static)

![express static](/img/blog/express-static.png)

#### config文件夹

index.js是主要文件
主要是配置开发环境与生产环境的文件路径配置

请求代理 `http-proxy-middleware` 插件


     proxyTable: {
        '/api':{
            target: 'http://test.zyjx.ucmed.cn/',//接口域名
            changeOrigin: true,  //是否跨域
            pathRewrite: {
              '^/api': ''  //需要rewrite重写的,
            }
        }
    },

这样，我们就可以在本地的开发中请求到后台或者测试、预生产等上面的接口。
注：本地开发不建议连接到生产环境上的接口

由于不再是之前的那种Eclipse的本地开发，而是完全前后端分离式的开发，所以为了便于前端的调试，我们申请了一个测试服务器，将请求直接代理到了测试服务器上。后台的小伙伴们只需要将自己的代码更新到测试服务器上面。前端同学就可以进行对接调试

**问题：由于是在老项目上分块进行重构**，登录页面没有重构之前怎么登录？
由于跨域访问的限制，其实主要也是登录状态的解决。由于在不同域的情况下，登录状态的问题是否需要改变？
开发阶段前端是通过后台代理进行跨域请求，所以不会出现跨域问题，但是由于登录状态的判定失败，跳转登录页面失败，因为这个属于前端页面请求
，所以被禁止

#### bulid文件夹

主要是一些webpack的配置文件，平常开发中基本不会改变
注：当你要使用一些新的东西，比如sass、Es6、less等一些目前不被普遍支持的新特性的时候，就需要添加相应的配置，才能够在打包的时候，被webpack正常的编译为被支持的文件。

比如Vue 2.0在IE11中页面空白的问题及解决方案 [babel-polyfill](https://hukangwei.github.io/blog/2017/09/15/vue-ie11/)

#### src文件夹

开发时代码基本都是在这里面，这里面又分很多功能文件夹。

![src文件夹结构](/img/blog/src-li.png)

**assets文件夹**---------静态资源所在
**components**-----------通用组件所在
**config文件夹**---------通用方法、配置
**resource文件夹**-------axios请求配置、拦截器、错误处理（之所以单独作为一个文件夹，是想后期将页面的方法抽离出来，暂时只是一个想法）

#### axios配置

默认请求基路径为`/api`,在开发模式下代理里面又设置了重定向，当匹配到`/api`的时候开启代理模式，并将`/api`重置为`''`

最初的axios是我简单写的，但是总感觉不够友好，这方面可以讨论一下。

+ 比如可以继续对一些状态进行不同的设置，对于不同的404,500状态码跳转到不同的页面，对不同的错误进行处理。（这一块的处理，在老项目里面是后台跳转的，这块要改）
+ 错误状态信息字段，统一为errMsg还是resMsg
+ 登录超时状态码统一为401？

现在我是在请求的拦截器里面写了很多的错误处理，就我对这个axios的理解，他可以做到将所有比较统一的错误进行统一处理。
虽然现在还不是很完善，但是肯定会把这块完善的。

**注意：**所以大家不用在页面请求里进行一些重复性错误处理，在请求里面，大家只需要对成功状态进行处理。对于统一的错误处理统一写
不过有一点需要注意，那个成功请求里面也有错误的原因，而且还有一些特殊请求返回的数据跟大多数不一致。
所以，我并没有将所有resCode不等于200的请求当做错误处理，意思就是虽然我对这些情况进行了处理，但是并不没有throw到error里面，所以，对于一些特殊请求，大家依然可以在then里面处理。对于其他的大多数请求，大家还是需要在then里面写一些判断函数，不过只需要判断成功状态，对成功状态进行处理就好，当然，如果有一些个没有被拦截器处理，也不是成功请求的，大家可以告诉我。因为现在这个axios配置，我的理解也不深。反正就是大家多讨论吧

这一块的话，在我们的老项目里面，对于请求error的处理很是简单，大多数也只是提示了一下网络错误。意思就是，我们一切都是向着好的方向努力的，只会更好的

**补充**
这是后来完善过的

    //返回状态判断    
    axios.interceptors.response.use((res) => {
        const code = res.data.resCode
        if(code == 200) {
            return res;
        } else if(code == 401) {
            Message({
                showClose: true,
                type: 'error',
                message: "登录超时，请重新登录"
            });
            router.push({
                path: "/login"
            });
            return res;
        } else {
            // 若不是正确的返回code，且已经登录，就抛出错误
            Message({
                showClose: true,
                type: 'error',
                message: res.data.resMsg
            });
            return res
        }
    }, (err) => {
        if(err && err.response) {
            switch(err.response.status) {
                case 400:
                    err.message = '请求错误'
                    break
    
                case 401:
                    err.message = '未授权，请登录'
                    break
        
                case 403:
                    err.message = '拒绝访问'
                    break
        
                case 404:
                    err.message = `登录失效`
                    break
        
                case 408:
                    err.message = '请求超时'
                    break
        
                case 500:
                    err.message = '服务器内部错误'
                    break
        
                case 501:
                    err.message = '服务未实现'
                    break
        
                case 502:
                    err.message = '网关错误'
                    break
        
                case 503:
                    err.message = '服务不可用'
                    break
        
                case 504:
                    err.message = '网关超时'
                    break
        
                case 505:
                    err.message = 'HTTP版本不受支持'
                    break
        
                default:
            }
            Message({
                showClose: true,
                type: 'error',
                message: err.message
            });
            if(err.response.status == 401 || err.response.status == 403 || err.response.status == 404){
                router.push({
                  path: "/login"
                });
            }
            
        }
    });
     

**router文件夹**----------路由的设置、及左侧导航的设置、及滚动行为函数
**store文件夹**-----------vuex
**views 文件夹**---------页面文件所在

#### History模式

这个模式想要玩好，需要后台的[配置支持](https://www.zhihu.com/question/46630687)


**路由方面**：现在登录页、主页是一级路由，导航页面是2级路由，详情页面本来是要三级路由的。但是考虑到路由层级太多不太友好，嵌套层次太多维护不便。就选择了下面的笨方法

    {
        path: 'appraiseManage/activityDetail/:id',
        name: 'activityDetail',
        component:activityDetail,
        meta: {
          title: '评价管理'
        },
    },

这样路由的代码嵌套层级会简单点。

路由跳转到详情页面的时候，应该使用下面方式，一般传参的话有两种：`params`跟`query`，但是在我写的时候，发现`params`刷新会找不到id
所以，我们使用下面这种方式，`query`刷新不会丢失id

    <router-link :to="{ path: 'detail', query: { id: '01' }}">详情</router-link>
    <!-- 带查询参数，下面的结果为 /detail ?id=01-->


**sha256加密**

这次的加密换成了`sha256`，它是哈希算法的一种,加密后的结果是64位字符
`sha256`和`bcryptjs`一样都是第三方密码加密库
安装:`npm install js-sha256`

    let sha256 = require("js-sha256").sha256;    //引入sha256库
    let hash = sha256(this.registerForm.passWord);    //hash为加密后的密码

#### 重构规范

开发规范与代码规范不在赘述，这里只讲vue重构项目的规范

全局变量：
全局变量使用 vuex ，在`mutation-type.js` 里面声明常量 ，相关使用常量替代Mutation 事件类型

![Mutation介绍](/img/blog/mutation.png)

前期没把一些枚举类作为全局变量来写，这个作为一个优化的点以后讨论，可以考虑放在state里面存储
不过对于一些纯粹的全局数据，还是要放在vuex的state里面。eg：登录id、机构、姓名等等



**单文件组件开发**

现在的开发方式为 .vue后缀的单文件组件，css经过配置，可以编写sass，现在css必须全部用sass编写，代码量会少很多

**页面布局**

推荐使用element-UI的 layout布局方式。通过24栏布局的方式应该能够满足我们所有的需求，一定要自己写的话，推荐使用css3的Flex弹性布局来写
这里是阮一峰的[flex教程-语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html )

**插件**

大部分的插件都在element里面是存在的。
表单输入验证：这个可以借此机会对所有的表单验证方式进行统一，替换掉以前所有的通过信息提示进行的表单错误反馈。
     这个要讨论确定使用哪种方式。比如，是我们自己来写过滤方法，还是找一个插件进行统一
     （这个可以作为后期开发任务，在调研完成后可以对所有表单，统一修改）
     （已解决）
使用的验证插件是element使用的，验证规则详见[async-validator](https://github.com/yiminghe/async-validator)

文件上传：这个功能element-ui是有的，首先我们现在的图片上传是传哪个服务器，然后也是需要测试一波。
就是不确定能不能行，毕竟跟之前的图片上传插件是不一样的。
使用的是[cos-js-sdk-v4插件](https://cloud.tencent.com/document/product/436/8095)

时间插件：日期这块的话，也是完全使用element-UI的组件，这块的变化主要是在时间范围方面,将以前通过`datetimepicker`插件改掉

**请求示例**

    this.$confirm('是否删除该管理员', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          axios.post('/standardized/hospitalBase/deleteHospitalAdmin.htm',data)
          .then(function(res){
            const code = res.data.resCode;
            if(code == 200){
              vm.$message({
                showClose: true,
                message: '删除成功',
                type: 'success'
              });
              vm.tableData.splice($index,1)
            }
          })
          .catch(function(err){
            console.log(err);
          })
    
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });
        });



#### 传参数据处理问题

后来不知道在那个地方出现了这个问题，在之前使用jQuery的时候是通过ajax配置给解决的。
[jQuery $.ajax传递数组的traditional参数传递必须true](http://blog.csdn.net/ojackhao/article/details/24580437)
这种请求，在jquery封装的ajax里面，使用traditional:true 对数据进行传统的序列化

使用axios改写的时候，我们要怎么去解决。 

[qs官网](https://www.npmjs.com/package/qs)
[assert断言库](http://www.cnblogs.com/newh5/p/6579894.html)

因为我们不再写什么ajax，都是配置好的。但是这个问题归根结底是数据处理上的问题

如果不进行处理，那么我们发送

    baseIdList:[473,466]

在浏览器上显示的入参是

    baseIdList[]:473
    baseIdList[]:466


要用axios的请求配置参数paramsSerializer进行params序列化

**Get请求**

    You may use the arrayFormat option to specify the format of the output array:
    
    qs.stringify({ a: ['b', 'c'] }, { arrayFormat: 'indices' })
    // 'a[0]=b&a[1]=c'
    qs.stringify({ a: ['b', 'c'] }, { arrayFormat: 'brackets' })
    // 'a[]=b&a[]=c'
    qs.stringify({ a: ['b', 'c'] }, { arrayFormat: 'repeat' })
    // 'a=b&a=c'

解决办法：

    this.$axios.get('/standardized/base/deleteBase.htm',{
      params:{
        baseIdList:ids
      },
      paramsSerializer: function(params){
        return Qs.stringify(params, {indices: false})
      }
    })

Post请求

由于在axios里面我们已经对post请求进行过`stringify`处理
我们只需要

    if(config.method === 'post' || config.method === "put" || config.method === "delete") {
        config.data = qs.stringify(config.data, {
            indices: false
        });
    }

#### 杂七杂八

最后最页面添加加载动画，表格添加无数据图，导入文件组件提取，ECharts图表组件提取等等等





















