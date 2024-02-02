<script setup lang="ts">
import { computed, ref, watch } from 'vue';
import Resizer from './SplitResizer.vue';
import Pane from './SplitPane.vue';
export interface ISplitPaneProps {
    minPercent?: number;
    defaultPercent?: number;
    layout: 'vertical' | 'horizontal';
    resizerClass?: string;
}
defineOptions({
    name: 'split-pane-index'
});
const props = withDefaults(defineProps<ISplitPaneProps>(), {
    defaultPercent: 50,
    minPercent: 10
});

const emit = defineEmits<{
    resize: [percent: number];
}>();

const active = ref(false);
const hasMoved = ref(false);
const percent = ref(props.defaultPercent);
const type = ref(props.layout === 'vertical' ? 'width' : 'height');
const resizeType = ref(props.layout === 'vertical' ? 'left' : 'top');
const userSelect = computed(() => {
    return active.value ? 'none' : 'auto';
});
const cursor = computed(() => {
    return active.value ? (props.layout === 'vertical' ? 'col-resize' : 'row-resize') : 'auto';
});
watch(
    () => props.defaultPercent,
    (newValue) => {
        percent.value = newValue;
    }
);
const onClick = () => {
    if (!hasMoved.value) {
        percent.value = 50;
        emit('resize', percent.value);
    }
};
const onMouseDown = () => {
    active.value = true;
    hasMoved.value = false;
};
const onMouseUp = () => {
    active.value = false;
};
const onMouseMove = (e: any) => {
    if (e.buttons === 0 || e.which === 0) {
        active.value = false;
    }

    if (active.value) {
        let offset = 0;
        let target = e.currentTarget;
        if (props.layout === 'vertical') {
            while (target) {
                offset += target.offsetLeft;
                target = target.offsetParent;
            }
        } else {
            while (target) {
                offset += target.offsetTop;
                target = target.offsetParent;
            }
        }

        const currentPage = props.layout === 'vertical' ? e.pageX : e.pageY;
        const targetOffset =
            props.layout === 'vertical'
                ? e.currentTarget.offsetWidth
                : e.currentTarget.offsetHeight;
        const _percent = Math.floor(((currentPage - offset) / targetOffset) * 10000) / 100;

        if (_percent > props.minPercent && _percent < 100 - props.minPercent) {
            percent.value = _percent;
        }

        emit('resize', percent.value);
        hasMoved.value = true;
    }
};
</script>

<template>
    <div
        :style="{ cursor: cursor, userSelect: userSelect }"
        class="vue-splitter-container clearfix"
        @mouseup="onMouseUp"
        @mousemove="onMouseMove"
    >
        <pane
            class="splitter-pane splitter-paneL"
            :layout="layout"
            :style="{ [type]: percent + '%' }"
        >
            <slot name="paneL"></slot>
        </pane>

        <resizer
            :className="resizerClass"
            :style="{ [resizeType]: percent + '%' }"
            :layout="layout"
            @mousedown="onMouseDown"
            @click="onClick"
        ></resizer>

        <pane
            class="splitter-pane splitter-paneR"
            :layout="layout"
            :style="{ [type]: 100 - percent + '%' }"
        >
            <slot name="paneR"></slot>
        </pane>
        <div class="vue-splitter-container-mask" v-if="active"></div>
    </div>
</template>

<style scoped>
.clearfix:after {
    visibility: hidden;
    display: block;
    font-size: 0;
    content: ' ';
    clear: both;
    height: 0;
}

.vue-splitter-container {
    height: 100%;
    position: relative;
}

.vue-splitter-container-mask {
    z-index: 9999;
    width: 100%;
    height: 100%;
    position: absolute;
    top: 0;
    left: 0;
}
</style>
