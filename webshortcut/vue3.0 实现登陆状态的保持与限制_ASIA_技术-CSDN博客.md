# vue3.0 实现登陆状态的保持与限制_ASIA_技术-CSDN博客
使用的插件 vuex，axios 和 vue router

项目中部分网页不需要登陆就能访问比如登陆，注册，其他页面的访问则需保持登陆状态，下面这种

1\. 首先配置路由：**在 router.js 中添加 meta 区分**

比如登录注册页面，不需要登录即可进入，那么我们把 meta 中的 isLogin 标志设置为**false**

而在首页我们需要登录才能进入，那么我们把 meta 中的 isLogin 标志设置为**true**

这样我们就为进入各个路由是否需要登录做了区分。

2\. 在登陆请求成功后修改状态

````null
this.$axios.post('/login/', this.$qs.stringify({username: this.logindata.username,pwd: this.logindata.pass,if (res.data.msg == '该用户不存在') {        that.logindata.erusername = res.data.msg;    } else if (res.data.msg == '输入密码错误') {        that.logindata.erpass = res.data.msg;localStorage.setItem("Userid", res.data.userid);localStorage.setItem("Flag", "isLogin");        that.$store.dispatch("userLogin", true);.catch(function (error) {```

这个里为什么要使用that重新代替this 是因为箭头函数中this指向了上下文，不在本组件中，取不到组件中的相关的值

3.在vuex中，stor.js这样写

```null
export default new Vuex.Store({isLogin: state => state.isLogin,        userStatus(state, flag) {        setUser({commit}, flag) {            commit("userStatus", flag)```

重点来了~，在mian.js里，需要控制路由的跳转

```null
router.beforeEach((to, from, next) => {let getFlag = localStorage.getItem("Flag");let getid = localStorage.getItem("Userid");if (getFlag === "isLogin"&&getid !== null) {        store.state.isLogin = true;```

这样就已经完成了Vue的登录注册，当用户关闭浏览器或者第二天再次进入网站，用户依旧可以保持着登录的状态直到用户手动退出登录。

用户退出只需要localStorage.removeItem("Flag")，localStorage.removeItem("Userid")即可

错误路由的拦截，只要我们没有登陆，或者错误的路由，都会跳转登陆页面 

```null
if (!this.$route.matched.length) {let getFlag = localStorage.getItem("Flag");let getid = localStorage.getItem("Userid");if ((getid===null||getFlag !== "isLogin") && this.$route.path != '/logon') {this.$router.push("/loginweb");``` 
 [https://blog.csdn.net/qq_32018951/article/details/89286669](https://blog.csdn.net/qq_32018951/article/details/89286669)
````
