<template></template>
<script>
import { ref } from "vue";
export default {
  setup() {
    let showMenu = ref(false);
    let show = ref(false);
    const toggleNav = () => (showMenu.value = !showMenu.value);
    return { showMenu, show, toggleNav };
  },
};
</script>
