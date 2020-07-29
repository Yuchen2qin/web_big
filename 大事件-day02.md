线上 DEMO 项目地址：http://www.escook.cn:8086/

项目的 API 接口地址： https://www.showdoc.cc/escook?page_id=3707158761215217

### 首页

- iframe的使用

  - 1.右侧内容区域使用iframe标签，设置name属性，默认设置src为首页html路径

  ```html
  <iframe name="fm" src="/home/dashboard.html" frameborder="0"></iframe>
  ```

  - 2.a标签设置href属性，为点击打开的页面地址；设置target属性，值为iframe的name

  ```html
  <a href="/home/dashboard.html" target="fm">首页</a>
  ```

  - 3.这样就相当于点击a标签，在iframe的地方打开对应的页面

- 设置请求头Authorization属性

  - 在$.ajaxPrefilter中，判断请求url中是否含有/my，有的话则设置请求头

  ```javascript
  if (options.url.indexOf('/my/') !== -1) {
    options.headers = {
      Authorization: localStorage.getItem('token') || ''
    }
  }
  ```

- 控制用户访问权限

  - 当token没有或者失效时（也就是没有登陆时）不能跳转或请求任何接口，则清除本地存储并且跳转到登陆页面
  - 在$.ajaxPrefilter中配置complete回调函数即可

  ```javascript
  // 全局统一挂载 complete 回调函数
  options.complete = function(res) {
    // 在 complete 回调函数中，可以使用 res.responseJSON 拿到服务器响应回来的数据
    if (res.responseJSON.status === 1 && res.responseJSON.message === '身份认证失败！') {
      // 1. 强制清空 token
      localStorage.removeItem('token')
      // 2. 强制跳转到登录页面
      location.href = '/login.html'
    }
  }
  ```

### 个人信息

- form.val的使用，该函数是给form表单填充数据用的

  - 1.form标签上定义lay-filter

  ```html
  <form class="layui-form" lay-filter="formUserInfo">
  ```

  - 2.函数中调用

  ```javascript
  form.val('formUserInfo', res.data)
  ```

  - 3.注意
    - form.val第一个参数与lay-filter的属性值相同
    - 第二个参数为后台返回回来的数据对象，input的name需要与对象属性名相同

- iframe中的页面调用父级index页面的函数

  - window.parent.getUserInfo()
  - getUserInfo一定需要定义在全局，不要定义在入口函数中

### 注意点

- 使用`live server`打开，不要使用`Open In Default Browser`

- form标签lay-filter除非需要使用，否则不要添加
- layui的各种class样式不能删除
- 提交按钮不要添加 `type="button"`
- 重置按钮必须添加`type="reset"`
- 所有不需要跳转herf的a标签，一律herf这么定义`herf="javascript:;":`
- form.verify里不需要写layer.msg，只需要return "提示字符串" 就可以有提示
- input的name属性的值，需要与后台接口需要的属性名相同，这样才可以用$(this).serialize()



