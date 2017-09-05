---
title: Vue学习之组件Component
date: 2017-09-04 21:48:52
tags:
categories:
---
#这里写上文章显示的摘要

<!-- more -->

组件是Vue最强大的特点之一，帮助你扩展基本的HTML元素来封装可重用的代码。从更高层次来说，组件是自定义的元素，Vue的编译器向上面添加特定的行为。有些情况下，它们也可能是原生的HTML元素，上面带有`is`属性。

#### 使用组件

使用`Vue.component(tagName,options)`来注册全局的组件：

```

Vue.component('my-component', {
  // options
})

```

不必将每个组件都进行全局注册。可以使用`components`实例选项来进行局部注册，这样的话，注册的组件只能在组件作用域内可见。

```

var Child = {
  template: '<div>A custom component!</div>'
}
new Vue({
  // ...
  components: {
    // <my-component> will only be available in parent's template
    'my-component': Child
  }
})

```


传递给Vue构造器的大多数选项都可以永在组件中，但是有个特殊，`data`是函数。


#### 复合组件

组件通常是父子组件的关系，父子组件通信的方法：父组件通过`props`将数据传递给子组件，子组件通过`events`发送通知给父组件。

#### Props

每个组件实例都有自己的孤立作用域，意味着不能在子组件的模板中直接引用父组件的数据。可以使用`props`来将数据传递给子组件。

`prop`是自定义的属性，将父组件的信息传递下去。子组件需要显示地声明它希望从父组件中接收到哪些props，使用`props`选项。


```

Vue.component('child', {
  // declare the props
  props: ['message'],
  // just like data, the prop can be used inside templates
  // and is also made available in the vm as this.message
  template: '<span>{{ message }}</span>'
})

```

下面，传递字符串给上面定义的组件：

```

<child message="hello!"></child>

```

可以使用`v-bind`动态地将props绑定到父组件的数据上。父组件的数据发生改变的时候，会自动地流到子组件中。

```

<div>
  <input v-model="parentMsg">
  <br>
  <child v-bind:my-message="parentMsg"></child>
</div>

```

#### 自定义的事件



#### 使用槽(Slot)进行内容分发



