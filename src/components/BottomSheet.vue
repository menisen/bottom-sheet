<template>
  <!-- The sheet component -->
  <div
    id="sheet"
    ref="sheet"
    class="column items-center justify-end"
    aria-hidden="true"
  >
    <!-- Dark background for the sheet -->
    <div
      class="overlay"
      ref="overlay"
      :style="{ backgroundColor: overlayColor }"
    ></div>

    <!-- The sheet itself -->
    <div
      ref="contents"
      class="contents column"
      :style="{ borderRadius: rounded }"
    >
      <!-- Sheet controls -->
      <header class="controls">
        <!-- The thing to drag if you want to resize the sheet -->
        <div class="draggable-area" ref="draggable-area">
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

<script setup lang="ts">
import { ref, onMounted, beforeUnmount } from "vue";

export interface IProps {
  overlay?: boolean;
  maxWidth?: string;
  maxHeight?: number;
  clickToClose?: boolean;
  effect?: string;
  rounded?: string;
  swipeAble?: boolean;
  fullScreen?: boolean;
  overlayColor?: string;
  backgroundScrollable?: boolean;
  backgroundClickable?: boolean;
}

const props = withDefaults(defineProps<IProps>(), {
  overlay: true,
  maxWidth: "640px",
  maxHeight: 100,
  clickToClose: true,
  effect: "fx-default",
  rounded: "12px 12px 0 0",
  swipeAble: true,
  fullScreen: false,
  overlayColor: "rgba(0, 0, 0, 0.40)",
  backgroundScrollable: false,
  backgroundClickable: false,
});

const emit = defineEmits(["closed", "opened"]);

const dragPosition = ref();
const sheetHeight = ref();
const sheet = ref();
const sheetContents = ref();
const draggableArea = ref();

let body:any = null;
let contentHeight = 0;

const setSheetHeight = (value: number) => {
  sheetHeight.value = value;
  sheetContents.value.style.height = `min(${props.maxHeight}px, ${sheetHeight.value}vh)`;

  if (sheetHeight.value === 100) {
    sheetContents.value.classList.add("fullscreen");
  } else {
    sheetContents.value.classList.remove("fullscreen");
  }
};

const open = () => {
  setSheetHeight(contentHeight);
  setIsSheetShown(true);
  if (!props.backgroundScrollable) {
    document.body.style.overflow = "hidden";
  }
};

const close = () => {
  setIsSheetShown(false);
  emit("closed");
  document.body.style.overflow = "auto";
};

const closeByClickToBackground = () => {
  if (props.backgroundClickable) {
    setIsSheetShown(false);
    emit("closed");
    document.body.style.overflow = "auto";
  }
};

const setIsSheetShown = (value: boolean) => {
  sheet.value.setAttribute("aria-hidden", String(!value));
};

const touchPosition = (event: any) => {
  return event.touches ? event.touches[0] : event;
};

const onDragStart = (event: any) => {
  dragPosition.value = touchPosition(event).pageY;
  sheetContents.value.classList.add("not-selectable");
  draggableArea.value.style.cursor = document.body.style.cursor = "grabbing";
};

const onDragMove = (event: any) => {
  if (!props.swipeAble) return;
  if (dragPosition.value === undefined) return;

  const y = touchPosition(event).pageY;
  const deltaY = dragPosition.value - y;
  const deltaHeight = (deltaY / window.innerHeight) * 100;

  setSheetHeight(sheetHeight.value + deltaHeight);
  dragPosition.value = y;
};

const onDragEnd = () => {
  dragPosition.value = undefined;
  sheetContents.value.classList.remove("not-selectable");
  draggableArea.value.style.cursor = document.body.style.cursor = "";

  if (sheetHeight.value < contentHeight - 10) {
    close();
  } else {
    setSheetHeight(contentHeight);
    if (!props.backgroundScrollable) {
      document.body.style.overflow = "hidden";
    }
  }
};

const calculateHeight = () => {
  contentHeight = (Math.min(props.maxHeight, body?.offsetHeight) / window.innerHeight) * 100;
};
// const update = () => {
//   calculateHeight();
//   setSheetHeight(contentHeight);
// };

onMounted(() => {
  // sheet.value = document.querySelector(".sheet");
  sheetContents.value = document.querySelector(".contents");
  draggableArea.value = document.querySelector(".draggable-area");
  body = document.querySelector(".body")?.children[0];
  calculateHeight();

// Hide the sheet when clicking the background
  const el = document.querySelector(".overlay");
  if (el) {
    el.addEventListener("click", () => {
      closeByClickToBackground();
    });
  }

  draggableArea.value.addEventListener("mousedown", onDragStart);
  draggableArea.value.addEventListener("touchstart", onDragStart);

  window.addEventListener("mousemove", onDragMove);
  window.addEventListener("touchmove", onDragMove);

  window.addEventListener("mouseup", onDragEnd);
  window.addEventListener("touchend", onDragEnd);
})

beforeUnmount(() => {
  document.body.style.overflow = "auto";
  window.removeEventListener("mousemove", onDragMove);
  window.removeEventListener("touchmove", onDragMove);

  window.removeEventListener("mouseup", onDragEnd);
  window.removeEventListener("touchend", onDragEnd);
})

defineExpose({ open, close });

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
  background: #ffffff;

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
  background: #a8a6b3;
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

.row.justify-end,
.column.justify-end {
  justify-content: flex-end;
}

.row.items-center,
.column.items-center {
  align-items: center;
}

.column {
  display: flex;
  flex-direction: column;
}
</style>
