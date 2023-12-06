
<script setup lang="ts">
defineProps({
    figure: String
})
</script>

<template lang="pug">
button.figure
    | {{ figure }}
</template>