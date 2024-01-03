<script setup lang="ts">
const props = defineProps({
    class: {
        type: String,
    },
    layoutClass: {
        type: String,
    },
})
</script>

<template>
    <div class="slidev-layout default w-full h-full grid grid-cols-3" :class="props.layoutClass">
        <div class="col-span-2" :class="props.class">
            <slot />
        </div>
        <div class="col-span-1" :class="props.class">
            <slot name="right" />
        </div>
    </div>
</template>