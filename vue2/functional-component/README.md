# Vue 2 Functional Components

A functional component is a **stateless**, **lifecycle-free** component that is optimized for performance (representational component in React). Unlike regular components, functional components are simply functions that receive props and return Virtual DOM elements. This makes them lightweight and faster to render, especially in large lists.

## Table of Contents
- [Key Characteristics](#-key-characteristics)
- [How to Define a Functional Component](#-how-to-define-a-functional-component)
  - [Using the `functional: true` Option](#1️⃣-using-the-functional-true-option)
  - [Using JavaScript-only Syntax](#2️⃣-using-the-javascript-only-syntax)
- [How to Use a Functional Component](#-how-to-use-a-functional-component)
  - [Access Prototype in Template](#access-prototype-in-template)
  - [Access Props in Template](#access-props-in-template)
  - [Access Self-Declared Functions in Template](#access-self-declared-functions-in-template)
  - [Handle Attributes, Listeners, Classes, Styles Binding](#handle-attributes-listeners-classes-styles-binding)
  - [Use Locally-Registered Components](#use-locally-registered-components)
  - [Use Locally-Registered Directives](#use-locally-registered-directives)
  - [Use Emit to Communicate to Parent Component](#use-emit-to-communicate-to-parent-component)
  - [Used as a Wrapper Component (Template Syntax)](#used-as-a-wrapper-component-template-syntax)
  - [Used as a Wrapper Component (Render Function Syntax)](#used-as-a-wrapper-component-render-function-syntax)
- [Reference](#-key-reference)

## 🔹 Key Characteristics:
1. **Stateless** – No `data` properties.
2. **Lifecycle-Free** – Does not have lifecycle hooks like `created`, `mounted`, etc.
3. **Enabled by the `functional` keyword** – Must be explicitly declared as `functional: true` in the component options.
4. **More Performant** – Since they lack reactivity overhead, functional components are **cheaper to render** and ideal for **large datasets**.

## 🔹 How to Define a Functional Component:
### 1️⃣ Using the `functional: true` Option
```vue
<template functional>
  <div class="message">
    {{ props.text }}
  </div>
</template>
```

### 2️⃣ Using the JavaScript-only syntax:
```vue
export default {
  functional: true,
  props: {
    text: String
  },
  render(h, { props }) {
    return h('div', { class: 'message' }, props.text);
  }
};
```

## 🔹 How to Use a Functional Component:

### Access Prototype in Template

```vue
<template functional>
  <div class="message-detail-mobile">
    <span class="title-black-SB-18">{{ parent.$t('internalMessage') }}</span>
  </div>
</template>
```

### Access Props in Template

```vue
<template functional>
  <div class="message-detail">
    <Row type="flex">
      <Col>
        <h5 class="message-detail__title">{{ props.title}}</h5>
      </Col>
    </Row>
  </div>
</template>
<script>
export default {
  name: 'MessageItem',
  props: {
    title: {
      type: String,
      required: true
    }
  }
}
</script>
```

### Access Self-Declared Functions in Template

```vue
<template functional>
  <div class="line-checkbox">
    <span class="title-red-SB-14">{{ $options.round(props.speed) }}ms</span>
  </div>
</template>
<script>
import { round } from '@/util/Number.js'
export default {
  name: 'LineCheckBox',
  props: {
    speed: {
      type: Number,
      required: true
    }
  },
  round
}
</script>
```

### Handle Attributes, Listeners, Classes, Styles Binding

```vue
<template functional>
  <div
    v-bind="data.attrs"
    v-on="listeners"
    :class="[data.staticClass, data.class]"
    :style="[data.staticStyle, data.style]"
  >
    <h1>Test</h1>
  </div>
</template>
```

### Use Locally-Registered Components

```vue
<template functional>
  <div>
    <GradientBtn />
  </div>
</template>

<script>
import Vue from 'vue'
import GradientBtn from '@/path/to/GradientBtn.vue'
Vue.component('GradientBtn', GradientBtn)
</script>
```

### Use Locally-Registered Directives

```vue
<template functional>
  <div>
    <!-- Apply the custom `fade-in` directive -->
    <img
      :src="props.imgUrl"
      alt="ranking img"
      v-fade-in
    />
  </div>
</template>

<script>
import Vue from 'vue';

Vue.directive('fade-in', {
  bind(el) {
    el.style.opacity = 0;
    el.style.transition = 'opacity 1s linear';
    el.onload = () => {
      el.style.opacity = 1;
    }
  }
})

export default {
  name: 'RankingCard',
  props: {
    imgUrl: {
      type: String,
      required: true,
    },
  },
};
</script>
```

### Use Emit to Communicate to Parent Component

```vue
<template functional>
  <div class="pledge-card">
    <Row type="flex" align="middle">
      <Col>
        <img :src="props.data.imgUrl" alt="pledge-card-img" />
      </Col>
      <Col>
        <h4>{{ props.data.itemName }}</h4>
        <Button @click="listeners['viewDetails'](props.data)">View Details</Button>
      </Col>
    </Row>
  </div>
</template>

<script>
export default {
  name: 'PledgeCard',
  props: {
    data: Object,
    loading: Boolean
  }
}
</script>
```

### Used as a Wrapper Component (Template Syntax)

```vue
<template functional>
  <component
    :is="parent.$isPhone ? injections.components.MobileBanner : injections.components.DesktopBanner"
  />
</template>
<script>
const MobileBanner = () => import('@/pc/components/index/MobileBanner')
const DesktopBanner = () => import('@/pc/components/index/DesktopBanner')
export default {
  inject: {
    components: {
      default: {
        MobileBanner,
        DesktopBanner
      }
    }
  }
}
</script>
```

### Used as a Wrapper Component (Render Function Syntax)

This approach opens up additional possibilities:
1. Programmatically choose one of several other components to delegate to
2. Manipulate children, props, or data before passing them on to a child component (higher order function in React)

```vue
<script>
var EmptyList = { /* ... */ }
var TableList = { /* ... */ }
var OrderedList = { /* ... */ }
var UnorderedList = { /* ... */ }
export default {
  name: 'smart-list',
  functional: true,
  props: {
    items: {
      type: Array,
      required: true
    },
    isOrdered: Boolean
  },
  render: function (createElement, context) {
    function appropriateListComponent () {
      var items = context.props.items

      if (items.length === 0)           return EmptyList
      if (typeof items[0] === 'object') return TableList
      if (context.props.isOrdered)      return OrderedList

      return UnorderedList
    }

    return createElement(
      appropriateListComponent(),
      context.data,
      context.children
    )
  }
}
</script>
```

## 🔹 Key Reference:
- [Official Doc](https://v2.vuejs.org/v2/guide/render-function#Functional-Components)
- [Functional components in Vue.js (2.x)](https://dev.to/vhoyer/functional-components-in-vue-js-20fl)
- [Functional single file component with components option](https://github.com/vuejs/vue/issues/7492)
- [揭秘 Vue.js 九个性能优化技巧](https://link.juejin.cn/?target=https%3A%2F%2Fmp.weixin.qq.com%2Fs%3F__biz%3DMzI3NTM5NDgzOA%3D%3D%26mid%3D2247491159%26idx%3D1%26sn%3Dd1d78bdd47a12395f098a234e35c0c75%26chksm%3Deb04262edc73af38013e0b06796672b30068291be3299a88889f577642dae47e3206637c6c87%26token%3D431470234%26lang%3Dzh_CN%23rd)
