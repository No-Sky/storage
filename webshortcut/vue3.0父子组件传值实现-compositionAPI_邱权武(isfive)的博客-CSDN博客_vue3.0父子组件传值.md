# vue3.0父子组件传值实现-compositionAPI_邱权武(isfive)的博客-CSDN博客_vue3.0父子组件传值
![](https://csdnimg.cn/release/blogv2/dist/pc/img/original.png)

[南易武痴](https://blog.csdn.net/qq_38494372) 2020-08-09 16:27:24 ![](https://csdnimg.cn/release/blogv2/dist/pc/img/articleReadEyes.png)
 3722 ![](https://csdnimg.cn/release/blogv2/dist/pc/img/tobarCollect.png)
 收藏  1 

版权声明：本文为博主原创文章，遵循 [CC 4.0 BY-SA](http://creativecommons.org/licenses/by-sa/4.0/) 版权协议，转载请附上原文出处链接和本声明。

## vue3.0 父子组件传值实现 - compositionAPI

父组件 Father.vue

```javascript
<template>
  <div>
    父组件
    <h1>{{ count }}</h1>
    <button @click="increment">父组件+</button>
    <child :count="count" @increment:count="increment"></child>
  </div>
</template>

<script>
import Child from "./Child.vue";
import { ref } from "vue";
export default {
  components: {
    Child,
  },
  setup() {
    const count = ref(0);
    const increment = () => {
      count.value++;
    };
    return {
      count,
      increment,
    };
  },
};
</script>

<style></style>


```

子组件 Chlid.vue

```javascript
<template>
  <div>
    子组件
    <h1>{{newCount}}</h1>
    <button @click="increment">子组件+</button>
  </div>
</template>

<script>
import {computed} from "vue"
export default {
  props: {
    count: Number,
  },
  setup(props, { emit }) {
    const newCount = computed({
      get: () => props.count,
      set: (value) => emit("increment:count"),
    });
    const increment=()=>{
        newCount.value++
    }
    return {
      newCount,
      increment,
    };
  },
};
</script>

<style></style>



```

 [https://blog.csdn.net/qq_38494372/article/details/107895698](https://blog.csdn.net/qq_38494372/article/details/107895698)
