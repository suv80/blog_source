---
title: 'vue3.x结合typescript初体验'
date: 2020-05-29 09:54:35
tags: [typesrcipt,vue]
published: true
hideInList: false
feature: 
isTop: false
---
## 一、Vue3.0 的设计目标

- 更小\更快 - Vue 3.0大小大概减少一半，只有10kB
- 加强TypeScript支持
- 加强API设计一致性 - 易读
- 提高自身可维护性
- 开放更多底层功能

vue3.x 采用Function-based API 形式组织代码，使其更容易压缩代码且压缩效率也更高,由于 修改了组件的声明方式，以函数组合的方式完成逻辑，天然与typescript 结合。（vue2.x中的组件是通过声明的方式传入一系列options的，所以在2.x下使用typeScript 需要装饰器的方式vue-class-component才行）

```html
// vue2.x 要想使用ts 需要这样处理,详见官方文档 https://cn.vuejs.org/v2/guide/typescript.html
<script lang="ts">
    import Vue from 'vue'
    import Component from 'vue-class-component'
    @Component
    export default class App extends Vue {}
</script>
```

## 二、typescript 有什么优点

### 1、增加代码的可读性与可维护性

大部分函数看类型定义就知道是干嘛的
静态类型检查，减少运行时错误

### 2、社区活跃，大牛都在用

在vue3热潮下，之后typescript要成为主流，快学

## 三、搭建vue3.x + typescript + vue-router 环境

### 1、全局安装vue-cli

`npm install -g @vue/cli`

### 2、初始化vue项目

`vue create vue-next-project`

这里在输入命令后，需要选择Manually select features, 至少要把 babel typescript router 选上，（vuex 看自身情况是否需要）。如下图:

![](http://blog.mayiya.vip/post-images/1590717583324.jpg)

### 3、升级为vue3.x项目

```bash
cd vue-next-project
vue add vue-next
```

这个命令会自动帮你把vue2.x升级到vue3.x ，包括项目中对应的依赖进行升级与模板代码替换，像：vue-router vuex

完成以上三步主体环境算搭建完成，看刚才创建的目录里多了个tsconfig.json 配置文件，可以根据自身与项目需要，进行配置。

接下来需要简单处理一下，使其支持typescript形式。（模板cli还没完善typescript的模板代码）

![](http://blog.mayiya.vip/post-images/1590717689555.jpg)

- 将shims-vue.d.ts文件中的内容修改一下，这步操作应该会少了一些报错。

```javascript
// declare module "*.vue" {
//   import Vue from 'vue';
//   export default Vue;
// }
declare module "*.vue" {
    import {defineComponent} from 'vue'
    const Component: ReturnType<typeof defineComponent>;
    export default Component
}
```

- 组件里使用需声明 script lang="ts" ,然后用defineComponent进行包裹,即可。

```javascript
<script lang="ts">
import {
  defineComponent,
  ref,
  onMounted,
  onUpdated,
  onUnmounted,
} from "vue";

export default defineComponent({
  name: "hello world",
  props: {},
  setup(props: any) {
    const count = ref(0);
    const increase = (): void => {
      count.value++;
    };
    function test(x: number): string {
      return props.toString();
    }
    test(1);
    // test('number');
    // 生命周期
    onMounted(() => {
      console.log("mounted vue3 typescript");
    });
    onUpdated(() => {
      console.log("updated vue3 typescript");
    });
    onUnmounted(() => {
      console.log("onUnmounted vue3 typescript");
    });
    // 暴露给模板
    return {
      count,
      increase
    };
  },
});

</script>
```
生命周期对应

![](http://blog.mayiya.vip/post-images/1590717822549.jpg)

## 四、附上学习vue-next 与typescript 的官方秘籍

- [composition-api](https://app.yinxiang.com/OutboundRedirect.action?dest=https%3A%2F%2Fcomposition-api.vuejs.org%2Fzh%2Fapi.html%23setup)
- [typescript](https://app.yinxiang.com/OutboundRedirect.action?dest=https%3A%2F%2Fwww.tslang.cn%2F)
- [typescript教程](https://app.yinxiang.com/OutboundRedirect.action?dest=https%3A%2F%2Fts.xcatliu.com%2Fintroduction%2Fwhat-is-typescript)
  
## 五、不想搭建你也可以直接拉去github demo

[vue3+typescript环境](https://app.yinxiang.com/OutboundRedirect.action?dest=https%3A%2F%2Fgithub.com%2Fkatoto%2Fvue3-typescipt)

---
转摘自：[掘金](https://juejin.im/post/5ec78ec451882543345e7fb9)