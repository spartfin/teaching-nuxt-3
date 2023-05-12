<template>
    <NuxtLink class="underline font-medium" :href="firstLesson.path">
        Перейти к первому уроку
    </NuxtLink>
</template>

<script setup lang="ts">
const firstLesson = await useFirstLesson();
</script>
