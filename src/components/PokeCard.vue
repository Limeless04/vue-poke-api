<template>
  <div class="col">
    <div class="card" style="width: 11rem">
      <img :src="imgUrl + getPokeId + '.png'" />
      <div class="card-body text-center">
        <h5 class="card-title" @click="$emit('getName', displayName)">
          {{ displayName }}
        </h5>
      </div>
    </div>
  </div>
</template>

<script>
import { computed, onMounted, ref } from "vue";
export default {
  props: ["pokeName", "pokeUrl"],
  setup(props) {
    const imgUrl = ref(
      "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/"
    );
    const pokeImg = ref();

    const displayName = computed(() => {
      return props.pokeName[0].toUpperCase() + props.pokeName.substring(1);
    });

    const getPokeId = computed(() => {
      return props.pokeUrl
        .split("/")
        .filter(function (part) {
          return !!part;
        })
        .pop();
    });

    // onMounted(() => {
    //   getImg()
    // });
    // onMounted(async () => {
    //   const imgData = await fetch(imgUrl + `${getPokeId}`
    //   ).then((res) => res.json());
    //   console.log(imgData)
    // });

    return {
      displayName,
      getPokeId,
      imgUrl,
    };
  },
};
</script>

<style>
</style>