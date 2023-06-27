<template>
  <!-- The sheet component -->
  <div id="sheet" class="column items-center justify-end" aria-hidden="true">
    <!-- Dark background for the sheet -->
    <div class="overlay" :style="{backgroundColor: overlayColor}"></div>

    <!-- The sheet itself -->
    <div class="contents column" :style="{borderRadius: rounded}">
      <!-- Sheet controls -->
      <header class="controls">
        <!-- The thing to drag if you want to resize the sheet -->
        <div class="draggable-area">
          <div class="draggable-thumb"></div>
        </div>
      </header>

      <!-- Body of the sheet -->
      <main class="body fill column">
        <div>
          <slot></slot>
        </div>
      </main>
    </div>
  </div>
</template>

<script>
export default {
  name: 'BottomSheet',
  props: {
    maxWidth: {
      type: String,
      default: '100%'
    },
    maxHeight: {
      type: Number,
      default: 100
    },
    clickToClose: {
      type: Boolean,
      default: true
    },
    effect: {
      type: String,
      default: 'fx-fadein-scale'
    },
    rounded: {
      type: String,
      default: '12px 12px 0 0'
    },
    swipeAble: {
      type: Boolean,
      default: true
    },
    isFullScreen: {
      type: Boolean,
      default: false
    },
    backgroundScrollable: {
      type: Boolean,
      default: false
    },
    backgroundClickable: {
      type: Boolean,
      default: true
    },
    overlayColor: {
      type: String,
      default: 'rgba(0, 0, 0, 0.40)'
    },
  },
  data() {
    return {
      dragPosition: undefined,
      sheetHeight: undefined,
      $: undefined,
      sheet: undefined,
      sheetContents: undefined,
      draggableArea: undefined,
      body: undefined,
      contentHeight: 0,
    }
  },
  methods: {
    setSheetHeight(value) {
      console.log(value)
      this.sheetHeight = value
      this.sheetContents.style.height = `min(${this.maxHeight}px, ${this.sheetHeight}vh)`

      if (this.sheetHeight === 100) {
        this.sheetContents.classList.add("fullscreen")
      } else {
        this.sheetContents.classList.remove("fullscreen")
      }
    },
    openModal() {
      this.setSheetHeight(this.contentHeight)
      this.setIsSheetShown(true)
      if (!this.backgroundScrollable) {
        document.body.style.overflow = 'hidden'
      }
    },
    closeModal() {
      this.setIsSheetShown(false)
      this.$emit('closed')
      document.body.style.overflow = 'auto'
    },
    closeByClickToBackground() {
      if (this.backgroundClickable) {
        this.setIsSheetShown(false)
        this.$emit('closed')
        document.body.style.overflow = 'auto'
      }
    },
    setIsSheetShown(value) {
      this.sheet.setAttribute("aria-hidden", String(!value))
    },
    touchPosition(event) {
      return event.touches ? event.touches[0] : event
    },
    onDragStart(event) {
      this.dragPosition = this.touchPosition(event).pageY
      this.sheetContents.classList.add("not-selectable")
      this.draggableArea.style.cursor = document.body.style.cursor = "grabbing"
    },
    onDragMove(event) {
      if (!this.swipeAble) return
      if (this.dragPosition === undefined) return

      const y = this.touchPosition(event).pageY
      const deltaY = this.dragPosition - y
      const deltaHeight = deltaY / window.innerHeight * 100

      this.setSheetHeight(this.sheetHeight + deltaHeight)
      this.dragPosition = y
    },
    onDragEnd() {
      this.dragPosition = undefined
      this.sheetContents.classList.remove("not-selectable")
      this.draggableArea.style.cursor = document.body.style.cursor = ""

      if (this.sheetHeight < this.contentHeight - 10) {
        this.closeModal()
      } else {
        this.setSheetHeight(this.contentHeight)
        if (!this.backgroundScrollable) {
          document.body.style.overflow = 'hidden'
        }
      }
    },
    calculateHeight() {
      this.contentHeight = Math.min(this.maxHeight, this.body?.offsetHeight) / window.innerHeight * 100
    },
    updateHeight() {
      this.calculateHeight()
      this.setSheetHeight(this.contentHeight)
    }
  },
  mounted() {
    this.$ = document.querySelector.bind(document)

    this.openSheetButton = this.$("#open-sheet")
    this.sheet = this.$("#sheet")
    this.sheetContents = this.sheet.querySelector(".contents")
    this.draggableArea = this.sheet.querySelector(".draggable-area")
    this.body = this.sheet.querySelector(".body")?.children[0]
    this.calculateHeight()


// Hide the sheet when clicking the background
    this.sheet.querySelector(".overlay").addEventListener("click", () => {
      this.closeByClickToBackground()
    })


    this.draggableArea.addEventListener("mousedown", this.onDragStart)
    this.draggableArea.addEventListener("touchstart", this.onDragStart)

    window.addEventListener("mousemove", this.onDragMove)
    window.addEventListener("touchmove", this.onDragMove)

    window.addEventListener("mouseup", this.onDragEnd)
    window.addEventListener("touchend", this.onDragEnd)
  }
}
</script>

<style scoped>
body {
  -webkit-tap-highlight-color: transparent;
}

#sheet {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 2;
  visibility: visible;
  transition: opacity 0.5s, visibility 0.5s;
}

#sheet[aria-hidden="true"] {
  opacity: 0;
  visibility: hidden;
  pointer-events: none;
}

#sheet .overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: -1;
}

#sheet .contents {

  background: #FFFFFF;

  position: relative;
  overflow-y: hidden;

  --default-transitions: transform 0.5s, border-radius 0.5s;

  transition: var(--default-transitions);
  transform: translateY(0);

  max-height: 100vh;
  width: 100%;

  box-sizing: border-box;
}

#sheet .contents:not(.not-selectable) {
  transition: var(--default-transitions), height 0.5s;
}

#sheet .contents.fullscreen {
  border-radius: 0;
}

#sheet[aria-hidden="true"] .contents {
  transform: translateY(100%);
}

#sheet .draggable-area {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  width: 100%;
  cursor: grab;
  padding: 8px 0 40px;
}

#sheet .draggable-thumb {
  width: 40px;
  height: 4px;
  background: #A8A6B3;
  border-radius: 4px;
  margin: auto;
}

#sheet .close-sheet {
  position: absolute;
  right: 0;
  top: 0;
  border: none;
}

#sheet .body {
  height: 100%;
  overflow-y: auto;
  gap: 20px;
}

.row.justify-end, .column.justify-end {
  justify-content: flex-end;
}

.row.items-center, .column.items-center {
  align-items: center;
}

.column {
  display: flex;
  flex-direction: column;
}
</style>