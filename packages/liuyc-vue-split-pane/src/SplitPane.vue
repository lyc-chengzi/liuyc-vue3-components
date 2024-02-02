<script setup lang="ts">
import { computed } from 'vue';
defineOptions({
    name: 'split-pane'
});
const props = defineProps<{
    layout: 'vertical' | 'horizontal';
    className?: string;
}>();
const classes = computed(() => {
    return ['splitter-pane', props.layout, props.className].join(' ');
});
</script>
<template>
    <div :class="classes">
        <slot></slot>
    </div>
</template>

<style scoped>
.splitter-pane.vertical.splitter-paneL {
    position: absolute;
    left: 0px;
    height: 100%;
    padding-right: 3px;
}

.splitter-pane.vertical.splitter-paneR {
    position: absolute;
    right: 0px;
    height: 100%;
    padding-left: 3px;
}

.splitter-pane.horizontal.splitter-paneL {
    position: absolute;
    top: 0px;
    width: 100%;
}

.splitter-pane.horizontal.splitter-paneR {
    position: absolute;
    bottom: 0px;
    width: 100%;
    padding-top: 3px;
}
</style>
