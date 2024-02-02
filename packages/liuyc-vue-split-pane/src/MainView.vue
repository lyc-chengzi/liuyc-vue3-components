<script setup lang="ts">
import SplitPane from './SplitPaneIndex.vue';
</script>
<template>
    <split-pane :min-percent="20" :default-percent="30" layout="vertical">
        <template v-slot:paneL> A </template>
        <template v-slot:paneR> B </template>
    </split-pane>
</template>
<style>
html,
body {
    width: 100%;
    height: 100%;
    margin: 0;
    padding: 0;
}
#app {
    height: 100%;
}
</style>
