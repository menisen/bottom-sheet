[Example](https://anyprinter.ru/logo.jpg)

# Bottom Sheet on Vue
![Size](https://img.shields.io/bundlephobia/minzip/@webzlodimir/vue-bottom-sheet)
![Downloads](https://img.shields.io/npm/dt/@webzlodimir/vue-bottom-sheet)
![Version](https://img.shields.io/npm/v/@webzlodimir/vue-bottom-sheet)

A nice clean and touch-friendly bottom sheet component based on [Vue.js](https://vuejs.org/)

[//]: # (- :clapper: [Demo]&#40;https://vaban-ru.github.io/vue-bottom-sheet-demo/&#41;)

[//]: # (- :open_book: [Документация на русском]&#40;https://github.com/vaban-ru/vue-bottom-sheet/blob/master/README_RU.MD&#41;)

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
import "@nya4om/bottom-sheet/style.css"
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
import "@nya4om/bottom-sheet/style.css"
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

[//]: # (You can see all the effects on the [demo page]&#40;https://vaban-ru.github.io/vue-bottom-sheet-demo/&#41;)