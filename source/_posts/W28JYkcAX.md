---
title: 'vue-router 之 keep-alive'
date: 2021-04-09 16:26:45
tags: [vue,vue-router,keep-alive]
published: true
hideInList: false
feature: 
isTop: false
---
## keep-alive 简介

`keep-alive` 是 Vue 内置的一个组件，可以使被包含的组件保留状态，或避免重新渲染。
 用法也很简单：



```xml
<keep-alive>
  <component>
    <!-- 该组件将被缓存！ -->
  </component>
</keep-alive>
```

#### props

- include - 字符串或正则表达，只有匹配的组件会被缓存
- exclude - 字符串或正则表达式，任何匹配的组件都不会被缓存



```kotlin
// 组件 a
export default {
  name: 'a',
  data () {
    return {}
  }
}
```



```xml
<keep-alive include="a">
  <component>
    <!-- name 为 a 的组件将被缓存！ -->
  </component>
</keep-alive>可以保留它的状态或避免重新渲染
```



```xml
<keep-alive exclude="a">
  <component>
    <!-- 除了 name 为 a 的组件都将被缓存！ -->
  </component>
</keep-alive>可以保留它的状态或避免重新渲染
```

## 遇见 vue-router

西湖雨好大，借把伞躲躲雨...
 `router-view` 也是一个组件，如果直接被包在 `keep-alive` 里面，所有路径匹配到的视图组件都会被缓存：



```xml
<keep-alive>
    <router-view>
        <!-- 所有路径匹配到的视图组件都会被缓存！ -->
    </router-view>
</keep-alive>
```

然而产品汪总是要改需求，拦都拦不住...

## 问题

如果只想 `router-view` 里面某个组件被缓存，怎么办？

- 使用 include/exclude
- 增加 router.meta 属性

#### 使用 include/exclude



```kotlin
// 组件 a
export default {
  name: 'a',
  data () {
    return {}
  }
}
```



```xml
<keep-alive include="a">
    <router-view>
        <!-- 只有路径匹配到的视图 a 组件会被缓存！ -->
    </router-view>
</keep-alive>
```

`exclude` 例子类似。

> 缺点：需要知道组件的 name，项目复杂的时候不是很好的选择

#### 增加 router.meta 属性



```dart
// routes 配置
export default [
  {
    path: '/',
    name: 'home',
    component: Home,
    meta: {
      keepAlive: true // 需要被缓存
    }
  }, {
    path: '/:id',
    name: 'edit',
    component: Edit,
    meta: {
      keepAlive: false // 不需要被缓存
    }
  }
]
```



```xml
<keep-alive>
    <router-view v-if="$route.meta.keepAlive">
        <!-- 这里是会被缓存的视图组件，比如 Home！ -->
    </router-view>
</keep-alive>

<router-view v-if="!$route.meta.keepAlive">
    <!-- 这里是不被缓存的视图组件，比如 Edit！ -->
</router-view>
```

> 优点：不需要例举出需要被缓存组件名称

#### 【加盐】使用 router.meta 拓展

假设这里有 3 个路由： A、B、C。

- 需求：
  - 默认显示 A
  - B 跳到 A，A 不刷新
  - C 跳到 A，A 刷新
- 实现方式
  - 在 A 路由里面设置 *meta* 属性：



```css
{
        path: '/',
        name: 'A',
        component: A,
        meta: {
            keepAlive: true // 需要被缓存
        }
}
```

- 在 B 组件里面设置 *beforeRouteLeave*：



```jsx
export default {
        data() {
            return {};
        },
        methods: {},
        beforeRouteLeave(to, from, next) {
             // 设置下一个路由的 meta
            to.meta.keepAlive = true;  // 让 A 缓存，即不刷新
            next();
        }
};
```

- 在 C 组件里面设置 *beforeRouteLeave*：



```jsx
export default {
        data() {
            return {};
        },
        methods: {},
        beforeRouteLeave(to, from, next) {
            // 设置下一个路由的 meta
            to.meta.keepAlive = false; // 让 A 不缓存，即刷新
            next();
        }
};
```

这样便能实现 B 回到 A，A 不刷新；而 C 回到 A 则刷新。

## 实现前进刷新，后退不刷新

感谢 [iceuncle](https://www.jianshu.com/u/beced864ad95) 分享的 [《vue实现前进刷新，后退不刷新》](https://juejin.im/post/5a69894a518825733b0f12f2)。

## 总结

路由大法不错，不需要关心哪个页面跳转过来的，只要 `router.go(-1)` 就能回去，不需要额外参数。

然而在非单页应用的时候，`keep-alive` 并不能有效的缓存了= =

## 参考

- [issues#811](https://github.com/vuejs/vue-router/issues/811)
- [vue#keep-alive](https://cn.vuejs.org/v2/api/#keep-alive)
- [vue2.0 keep-alive最佳实践](https://segmentfault.com/a/1190000008123035)

—— 2017/04/06 By Live， haze currently.



作者：於風聽語
链接：https://www.jianshu.com/p/0b0222954483
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。