# vue2-functional component

In Vue 2, a functional component is a **stateless**, **lifecycle-free** component optimized for performance(simply a function). It serves a similar role to **presentational components** in React. Since it lacks these overhead, it's more efficient—especially when rendering large lists.

We can use it by marking the template with **functional**


## Usage

### Access prototype in template

```vue
<template functional>
  <div class="message-detail-mobile">
	    <span class="title-black-SB-18">{{ parent.$t('internalMessage') }}</span>
  </div>
</template>
```

### Access props in template

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

### Access self-declared functions in template

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



