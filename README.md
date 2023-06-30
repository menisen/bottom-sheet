
# Bottom Sheet on Vue

A nice clean and touch-friendly bottom sheet component without library based on [Vue.js] 3 (https://vuejs.org/)

- :clapper: [Demo](https://bottom-sheet.neppe.kz/)

## Installation

### NPM

`npm install --save @nya4om/bottom-sheet`

### Yarn

`yarn add @nya4om/bottom-sheet`

## Usage

```vue
<template>
  <bottom-sheet ref="myBottomSheet">
    <h1>Lorem Ipsum</h1>
    <h2>What is Lorem Ipsum?</h2>
    <p>
      <strong>Lorem Ipsum</strong> is simply dummy text
    </p>
  </bottom-sheet>
</template>
<script>
import  BottomSheet from "@nya4om/bottom-sheet";
export default {
  components: {
    BottomSheet
  },
  methods: {
    open() {
      this.$refs.myBottomSheet.open();
    },
    close() {
      this.$refs.myBottomSheet.close();
    }
  }
}
</script>
```

Or add to main.js for use throughout the project
```js
import BottomSheet from "@nya4om/bottom-sheet";
import Vue from "vue";

Vue.use(BottomSheet);
```

## Props

| Prop  | Type    | Description                                                   | Example                         |
| ------------- |---------|---------------------------------------------------------------|---------------------------------|
| max-width  | String  | Set max-width of component card                               | `max-width="100%"`              |
| max-height  | Number  | Set max-height of component card on px                        | `max-height="600"`              |
| rounded  | String  | Round the top two corners of the sheet                        | `:rounded="12px 12px 0 0"`      |
| swipe-able  | Boolean | Enable or disable swipe to close                              | `:swipe-able="false"`           |
| overlay-color  | String  | Set overlay color                                 | `:overlay-color="rgba(0, 0, 0, 0.40)"`    |
| background-scrollable  | Boolean | Enable scroll                                                 | `:background-scrollable="true"` |
| background-clickable  | Boolean | Enable background click, doesn't work if `click-to-close=true` | `:background-clickable="true"`  |

## Events

| Event  | Description | Example |
| ------------- | ------------- | ------------- |
| opened  | Fire when card component is opened  | @opened="" |
| closed  | Fire when card component is closed  | @closed="" |

You can see all the effects on the [demo page](https://bottom-sheet.neppe.kz/)