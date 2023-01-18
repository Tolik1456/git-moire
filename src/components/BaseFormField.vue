<template>
  <label class="form__label">
    <slot></slot>
    <span class="error" v-if="error">{{ error }}</span>
  </label>
</template>

<script>
export default {
  props: ['title', 'error'],
};
</script>
