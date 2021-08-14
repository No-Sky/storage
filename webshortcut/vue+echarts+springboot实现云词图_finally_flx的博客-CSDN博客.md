# vue+echarts+springboot实现云词图_finally_flx的博客-CSDN博客
![](https://csdnimg.cn/release/blogv2/dist/pc/img/original.png)

[Kalinda Sharma](https://blog.csdn.net/finally_flx) 2020-03-14 09:08:34 ![](https://csdnimg.cn/release/blogv2/dist/pc/img/articleReadEyes.png)
 948 ![](https://csdnimg.cn/release/blogv2/dist/pc/img/tobarCollect.png)
 收藏  3 

版权声明：本文为博主原创文章，遵循 [CC 4.0 BY-SA](http://creativecommons.org/licenses/by-sa/4.0/) 版权协议，转载请附上原文出处链接和本声明。

## 1、options.js 数据文件

**（1）series 中 data 置空，数据从后端获取**  
**（2）其他界面设置信息在前端写定**  
![](https://img-blog.csdnimg.cn/20200314085133666.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZpbmFsbHlfZmx4,size_16,color_FFFFFF,t_70)

## 2、echartsCloud.vue

**1) 推荐使用 ref**  
![](https://img-blog.csdnimg.cn/20200314091047575.png)

**(2)chart 指向 ref， 初始化视图**  
![](https://img-blog.csdnimg.cn/20200314085332658.png)

![](https://img-blog.csdnimg.cn/20200314085445692.png)

**(3) 向后端发送请求获取数据**

**每次数据更改都需要执行 setOption() 操作**![](https://img-blog.csdnimg.cn/2020031408560551.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZpbmFsbHlfZmx4,size_16,color_FFFFFF,t_70)

**（4）执行初始化数据**  
![](https://img-blog.csdnimg.cn/20200314085946628.png)

**（5）在 mounted() 中执行**  
![](https://img-blog.csdnimg.cn/20200314090042912.png)

## 3、显示效果

![](https://img-blog.csdnimg.cn/20200314090122755.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZpbmFsbHlfZmx4,size_16,color_FFFFFF,t_70)

## 4、 注意事项

**（1）不推荐使用 id 绑定 DOM，使用 ref  
（2）this.nextTick()  
（3）初始化视图和数据的顺序  
（4）数据更改后 setOption()**

## 5、echartsCloud.vue 全部代码

```javascript
<template>
  <div>
    <div ref="myChart" class="containEcharts"></div>
  </div>
</template>
<script>
let baseURL = "http://localhost:8888";

import { optionThird } from "../../../../static/staticData/options";

export default {
  name: "echartsCloud",
  data() {
    return {
      chart: null
    };
  },

  mounted() {
    this.$nextTick(function() {
      this.initView();
      this.initData();
    });
  },
  methods: {
    initData() {
      this.selectEchartsCloud();
    },
    initView() {
      this.chart = this.$echarts.init(this.$refs.myChart);
    },

    
    selectEchartsCloud() {
      this.$http({
        url: baseURL + "/echartsCloud/selectEchartsCloud",
        methods: "post"
      })
        .then(response => {
          optionThird.series[0].data = response.data;
          console.log(optionThird);
          this.chart.setOption(optionThird);
        })
        .catch(function(error) {
          console.log(error);
        });
    }
  }
};
</script>

<style scoped>
.containEcharts {
  float: left;
  width: 550px;
  height: 250px;
  border: 1px solid #eee;
  border-radius: 5%;
}
</style>

```

 [https://blog.csdn.net/finally_flx/article/details/104854477](https://blog.csdn.net/finally_flx/article/details/104854477)
