---
layout: post
title:  "vue or quasar layout 구성 방법 3가지"
date:   2021-01-02 00:18:23 +0700
categories: [vue, quasar]
---
>vue 또는 quasar 에서 layout 방식에 대해 고민해 본다.
>많은 방식 중에 일반적인방식, 메타태그를 이용한방식, 마지막으로 dynamic 한 layout 구성 방식에 대해 살펴본다.
>해당 방식들의 차이를 고민해 보고 더 좋은 방법에 대해 고민해 보면 좋을 것 같다.

#### 테스트환경

```
quasar 에서 설명하여 route 부분이 lazy 방식으로 되있다는 점.
main.js 가 index.js 로 대체된다는 점이 일반적인 vue와 구성에 다른점이라 하겠다.

defaultLayout.vue 가 생성되어 있다.
```


### 1. 일반적인방식  

`defaultLayout.vue`
```javascript
<template>
	<router-view />
</template>
```

또는

```javascript
<template>
	<slot />
</template>
```

방식으로 layout 구성을 한다.

나머지 방식의 layout 도 `<slot>` 으로 작성한다.

`app.vue`
```javascript
<div id="q-app">
	<router-view :key="$route.fullPath" />
</div>
```
vue 기본 셋팅 + slot 을 이용한 layout 방식이라 하겠다.


### 2. meta tag

route 먼저 살펴보면


`route.js`
```javascript
...
{
  path: '/main',
  meta: { public: true, layout: 'default' },
  component: () => import('pages/Main.vue')
},
...
```


meta 부분에 layout 이 `default` 로 들어가 있는데,

해당 부분을 아래와 같이 app.vue 에서 받아 처리한다.

처리전에 layout component 를 미리 정한 naming rule 로 사용 할 수 있게 만들자.


`index.js or main.js`
```javascript
import DefaultLayOut from '../layouts/DefaultLayout.vue'
Vue.component('default-layout', DefaultLayOut)
```

app.vue 도 바뀌는 부분이 있는데

`app.vue`
```javascript
<div id="q-app">
  <component :is="layout">
    <router-view :key="$route.fullPath" />
  </component>
</div>
...
...
<script>
...
computed: {
  layout() {
    return (this.$route.meta.layout || "default") + "-layout";
  },
},
```

  
computed 를 추가 하고 route.js 에서 넘어온 layout을 component 로 만들자.

component로 감쌌으니 main.vue 에서 더이상 <DefaultLayout> 으로 내용을 감쌀 필요 없다.

그럼, 이제 route meta 에 정의한 layout 들만 바꿔서 원하는 layout 으로 변경할 수 있다.





### 3. dynamic

dynamic 은 .sync 를 사용하여 구현하며, 

하나의 페이지에서 경우에 따라 레이아웃을 변동해야 할 경우 유용하게 사용할 수 있다.

[sync 속성에 대한 더 자세한 내용](https://kr.vuejs.org/v2/guide/components-custom-events.html)  


`app.vue`  


  
```javascript
<div id="q-app">
  <component :is="layout">
    <router-view :key="$route.fullPath" :layout.sync="layout"/>
  </component>
</div>
...
<script>
data() {
    return {
      layout: `div`,
    };
  },
```

prop 에 대하여 양방향 바인딩 설정을 하였다.

layout 을 변경하고자 하는 vue page 에서

`main.vue`
```javascript
created() {
  this.$emit(`update:layout`, DefaultLayout);
},
```

이제 원하는 레이아웃으로 바꿀 수 있다.

하지만 3번 방식은 특별한 경우가 아니면 사용을 자제함이 좋다.

emit 한번에 app.vue 에서 모든 페이지의 layout을 바꾼다.

현재는 2번방식으로 사용중인데, 기존방식과 비교해 본다.

`route.js 기존`
```javascript
consultant
  {
    path: '/page1',
    name: 'page1',
    component: () => import('layouts/layout.vue'),
    meta: { public: true },
    children: [
      { path: '',   name: '1',  meta: { public: true }, component: () => import('pages/test/1.vue') },
      { path: '2',  name: '2',  meta: { public: true }, component: () => import('pages/test/2.vue') },
      { path: '3',  name: '3',  meta: { public: true }, component: () => import('pages/test/3.vue') },
      { path: '4',  name: '4',  meta: { public: true }, component: () => import('pages/test/4.vue') },
    ]
  },
```

`route.js 2번`
```javascript
{
  path: '/1',
  meta: { public: true, layout: 'default' },
  component: () => import('pages/test/1.vue')
},
{
  path: '/2',
  meta: { public: true, layout: 'main' },
  component: () => import('pages/test/2.vue')
},
```

2번 방식의 가장 큰 장점이라면, `구현 page 에서 더이상 layout 을 신경 쓸 필요없이 route 에서 이미 결정 한후 구현에만 집중 할 수 있다` 

더 이상 각 페이지에 layout 을 import 하지않고, 설계 단계에서 app.vue 와 route.js 

만의 작성으로 정할 수 있어서 좋은 방법이었던것 같다.

참고로 위 방법들은 lazy 로딩 방식이 아닐 때에도 동일한 방법으로 적용 가능하다. 




