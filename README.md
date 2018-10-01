# vue-slideshow-component
Vue Slideshow Component

```html
<template>
  <div id="app">
    
    <Slideshow v-if="gallery.length !== 0" :gallery="gallery"></Slideshow>

  </div>
</template>
```

```javascript
import { Slideshow } from 'vue-slideshow-component/src/index';

export default {
name: 'app',
  data() {
    return {
      gallery: [
        'image_url',
        'image_url',
        'image_url'
      ]
    }
  },
  components: {
    Slideshow
  }
}
```
