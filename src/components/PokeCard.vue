<template>
  <p @click="$emit('getName', displayName)">
    {{ displayName }}
  </p>
</template>

<script>
import { computed, ref } from "vue";
export default {
  props: ["pokeName"],
  setup(props) {

    const displayName = computed(() => {
      return props.pokeName[0].toUpperCase() + props.pokeName.substring(1);
    });

    return {
      displayName,
    };
  },
};
</script>

<style>
</style>