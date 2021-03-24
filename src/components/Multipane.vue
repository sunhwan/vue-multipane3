<template>
  <div :class="classnames" :style="{ cursor, userSelect }" @mousedown="onMouseDown">
    <slot></slot>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

const LAYOUT_HORIZONTAL: String = "horizontal";
const LAYOUT_VERTICAL: String = "vertical";
type Globals = "-moz-initial" | "inherit" | "initial" | "revert" | "unset";
type UserSelectProperty = Globals | "-moz-none" | "all" | "auto" | "contain" | "element" | "none" | "text";

export default defineComponent({
  name: 'Multipane',

  props: {
    layout: {
      type: String,
      default: LAYOUT_VERTICAL,
    },
  },

  data () {
    return {
      isResizing: false,
    }
  },

  computed: {
    classnames (): string[] {
      return [
        'multipane',
        'layout-' + this.layout.slice(0, 1),
        this.isResizing ? 'is-resizing' : '',
      ]
    },

    cursor (): string {
      return this.isResizing
        ? this.layout == LAYOUT_VERTICAL ? 'col-resize' : 'row-resize'
        : '';
    },

    userSelect (): UserSelectProperty | undefined {
      return this.isResizing ? "none" : undefined;
    },
  },

  methods: {
    onMouseDown({ target: resizer, pageX: initialPageX, pageY: initialPageY }: MouseEvent) {
      if (resizer instanceof HTMLDivElement && resizer?.className && resizer?.className.match('multipane-resizer')) {
        let self = this;
        let { $el: container, layout } = self;

        let pane = resizer.previousElementSibling;
        if (!(pane instanceof HTMLDivElement)) {
          return;
        }

        let {
          offsetWidth: initialPaneWidth,
          offsetHeight: initialPaneHeight,
        } = pane;

        let usePercentage = !!(pane.style.width + '').match('%');

        const { addEventListener, removeEventListener } = window;

        const resize = (initialSize: number | null = null, offset: number = 0) => {
          if (!(pane instanceof HTMLDivElement) || initialSize === null) {
            return;
          }

          if (layout == LAYOUT_VERTICAL) {
            let containerWidth = container.clientWidth;
            let paneWidth = initialSize + offset;
            let size = (pane.style.width = usePercentage
              ? paneWidth / containerWidth * 100 + '%'
              : paneWidth + 'px');

            return (pane.style.width = usePercentage
              ? paneWidth / containerWidth * 100 + '%'
              : paneWidth + 'px');
          }

          if (layout == LAYOUT_HORIZONTAL) {
            let containerHeight = container.clientHeight;
            let paneHeight = initialSize + offset;

            return (pane.style.height = usePercentage
              ? paneHeight / containerHeight * 100 + '%'
              : paneHeight + 'px');
          }
        };

        // This adds is-resizing class to container
        self.isResizing = true;

        // Resize once to get current computed size
        let size = resize();

        // Trigger paneResizeStart event
        self.$emit('paneResizeStart', pane, resizer, size);

        const onMouseMove = function({ pageX, pageY }: MouseEvent) {
          size =
            layout == LAYOUT_VERTICAL
              ? resize(initialPaneWidth, pageX - initialPageX)
              : resize(initialPaneHeight, pageY - initialPageY);

          self.$emit('paneResize', pane, resizer, size);
        };

        const onMouseUp = function() {
          if (!(pane instanceof HTMLDivElement)) {
            return;
          }

          // Run resize one more time to set computed width/height.
          size =
            layout == LAYOUT_VERTICAL
              ? resize(pane.clientWidth)
              : resize(pane.clientHeight);

          // This removes is-resizing class to container
          self.isResizing = false;

          removeEventListener('mousemove', onMouseMove);
          removeEventListener('mouseup', onMouseUp);

          self.$emit('paneResizeStop', pane, resizer, size);
        };

        addEventListener('mousemove', onMouseMove);
        addEventListener('mouseup', onMouseUp);
      }
    },
  },
});
</script>

<style lang="scss">
.multipane {
  display: flex;
  position: relative;
  &.layout-h {
    flex-direction: column;
  }
  &.layout-v {
    flex-direction: row;
  }
}

.multipane > div {
  position: relative;
  z-index: 1;
}

.multipane-resizer {
  display: block;
  position: relative;
  z-index: 2;
}

.layout-h > .multipane-resizer {
  width: 100%;
  height: 10px;
  margin-top: -10px;
  top: 5px;
  cursor: row-resize;
}

.layout-v > .multipane-resizer {
  width: 10px;
  height: 100%;
  margin-left: -10px;
  left: 5px;
  cursor: col-resize;
}
</style>