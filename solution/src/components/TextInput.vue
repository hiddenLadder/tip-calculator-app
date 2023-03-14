<script setup lang="ts">
interface Props {
  modelValue?: number | string;
  error?: string | undefined;
}
defineProps<Props>();
defineEmits(["update:modelValue"]);
</script>

<script lang="ts">
export default {
  inheritAttrs: false,
};
</script>

<template>
  <div
    class="bg-neutral-100 focus-within:outline-primary inline-flex outline outline-2 rounded text-neutral-400"
    :class="[error ? 'outline-red-600' : 'outline-transparent']"
  >
    <slot name="prepend" />
    <input
      class="border-none bg-neutral-100 font-bold flex-grow placeholder:text-neutral-500/40 text-neutral-500 focus:ring-0 text-right rounded p-1 pr-2"
      :value="modelValue"
      placeholder="0"
      @input="$emit('update:modelValue', ($event.target as HTMLInputElement).value)"
      type="text"
      :="$attrs"
    />
  </div>
</template>
