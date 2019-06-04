---
title: Svelte初看
date: 2019-06-04
tags: [JavaScript]
categories:
- JavaScript
comments: true
---

下面内容只是看了一遍svelte的入门文档，做了个对比和总结。

<!-- more -->

# 一些对比

||Vue|Svelte|
|---|---|---|
|单文件|.vue文件|.svelte文件|
|HTML|template包裹|1. html标签; 2. 不要求最外层包裹|
|变量绑定|:绑定和{% raw %}{{}}{% endraw %}|{}|
|样式作用域|scope|默认限制|
|插入HTML标签字符串|v-html="htmlString"|{@htmlString}|
|计算属性（变量响应）|computed/watch|$:语法|
|数组响应|代理原数组方法|手动重新赋值|
|子组件接收props|props属性|export关键字[示例1](#示例1)|
|props解构|v-bind="obj"|{...obj}|
|全部props|vm.$props|$$props|
|if|v-if="true" v-else|{% raw %}{#if true}{:else}{/if}{% endraw %}|
|for|v-for="(item, index) of list" :key="item.id"|{% raw %}{#each list as item, index (item.id)}{/each}{% endraw %}|
|直接使用promise|无|{% raw %}{#await promise then value}{/await}{% endraw %}[示例2](#示例2)|
|event|@或v-on:|on:|
|事件修饰符|.once(stop、prevent、capture、self、passive)|\|once(preventDefault、stopPropagation、passive、capture)|
|自定义事件|$emit|createEventDispatcher()返回的dispatch方法|
|双向绑定|v-model|bind:value|
|生命周期|全&钩子函数|onMount、onDestroy、beforeUpdate、afterUpdate、tick&import[示例3](#示例3)|
|状态管理|vuex(Redux)|store(RxJS)|
|操作DOM|$refs|指令|
|插槽|slot|slot|

-----

1. svelte单文件更接近html文件语法
2. html、js、css分离的更彻底
3. 通过编译器实现响应

svelte单文件中HTML标签直接写在最外层，因为不需要虚拟节点，也不需要必须有同一个根元素下。  
js脚本放在`script`标签中，样式放在`style`标签中，要求只是不能存在多个`script`标签。

和React统一的思路不同，但是比Vue分离的更彻底。Vue中每个组件都是一个对象，这个对象有data、props等属性，created、destroyed等钩子函数，html模板中只能使用data、props中变量，更多时候是按照Vue对象来实现的。  
Svelte中因为自身的真正响应式，对js代码的编写上限制很少，可以使用所有变量。生命周期也是通过引入了一个函数来实现的。

通过编译器实现响应，没有深入了解，Vue中只需要将一些变量做响应式，Svelte中所有变量都会响应，性能上不确定如何。

Await Blocks可以很清晰的分出loading、done、error状态，但是在HTML中使用{}划分block的方式，可能会显得HTML会有一些乱，应该会很难避免await block和if block。  
这块并不好处理，个人觉得，vue可能稍微好一点，React那种写入js中的可能才是最好的解决方案。

# 示例

#### 示例1 

```html
<!-- App.svelte -->
<script>
	import Nested from './Nested.svelte';
</script>

<Nested answer={42}/>
```

```html
<!-- Nested.svelte -->
<script>
    export let answer
    // 或者 export let answer = 0 给出默认值
</script>

<p>The answer is {answer}</p>
```

#### 示例2

```html
<!-- promise是一个promise对象 -->
{#await promise}
	<p>...waiting</p>
{:then number}
	<p>The number is {number}</p>
{:catch error}
	<p style="color: red">{error.message}</p>
{/await}

<!-- 不需要catch -->
{#await promise then value}
	<p>the value is {value}</p>
{/await}
```

#### 示例3

```html
<script>
import { onMount } from 'svelte'

onMount(() => {
    // 执行回调
})

</script>
```