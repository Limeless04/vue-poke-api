<script setup>
import PokeCard from "./components/PokeCard.vue";
import { reactive, ref } from "@vue/reactivity";
import Pagination from "./components/Pagination.vue";

import { computed, onMounted, watchEffect } from "@vue/runtime-core";

const filterText = ref("");
const pokemonDetail = ref("");
const perPage = ref(5);
const start = ref(0);
const end = ref(5);
const totalPage = ref(null);
const currentPage = ref(0);
const isDisable = ref(false);
const prevLink = ref(1);
const nextLink = ref(3);
const totalPoke = ref()


const getPokeName = (name) => {
  pokemonDetail.value = name;
};

const pokemonStore = reactive({
  list: [],
  filteredList: computed(() => {
    return pokemonStore.list.filter((pokes) => {
      return pokes.name.includes(filterText.value);
    });
  }),
  // pokeId: getID(pokemonStore.list)
});

watchEffect(async () => {
  const pokeData = await fetch(
    "https://pokeapi.co/api/v2/pokemon/?limit=" + `${perPage.value}`
  ).then((res) => res.json());
  pokemonStore.list = pokeData.results;
  totalPage.value = Math.ceil(pokeData.count / perPage.value);
  // changePage()
});

const pageNav = async (direction) => {
  if (direction === "prev") {
    start.value -= parseInt(perPage.value);
    end.value -= parseInt(perPage.value);
    const pokeData = await fetch(
      "https://pokeapi.co/api/v2/pokemon/?offset=" +
        `${start.value}` +
        "&limit=" +
        `${perPage.value}`
    ).then((res) => res.json());
    pokemonStore.list = pokeData.results;
  } else if (direction === "next") {
    start.value += parseInt(perPage.value);
    end.value += parseInt(perPage.value);
    const pokeData = await fetch(
      "https://pokeapi.co/api/v2/pokemon/?offset=" +
        `${start.value}` +
        "&limit=" +
        `${perPage.value}`
    ).then((res) => res.json());
    pokemonStore.list = pokeData.results
  } else if (!isNaN(direction)) {
    end.value = direction * parseInt(perPage.value)
    start.value = end.value - parseInt(perPage.value)
    const pokeData = await fetch(
      "https://pokeapi.co/api/v2/pokemon/?offset=" +
        `${start.value}` +
        "&limit=" +
        `${perPage.value}`
    ).then((res) => res.json());
    pokemonStore.list = pokeData.results;
  }
  currentPage.value = end.value / perPage.value;
};

onMounted(async () => {
  const pokeData = await fetch(
    "https://pokeapi.co/api/v2/pokemon/?limit=" + `${end.value}`
  ).then((res) => res.json());
  pokemonStore.list = pokeData.results;
  totalPage.value = Math.ceil(pokeData.count / perPage.value);
  totalPoke.value = parseInt(pokeData.count)
});
</script>

<template>
  <div class="container">
    <h1 class="text-center">Poke APi</h1>
    <p class="text-center">Selected <a class="btn badge bg-info">{{ pokemonDetail }}</a></p>
    <select v-model="perPage" class="form-select mb-4">
      <option value="5">5 per page</option>
      <option value="10">10 per page</option>
      <option value="20">20 per page</option>
    </select>
    <div class="input-group flex-nowrap mb-2">
      <span class="input-group-text" id="addon-wrapping">filter</span>
      <input
        type="text"
        class="form-control"
        placeholder="Filter Pokemon"
        v-model="filterText"
      />
    </div>
    <p>filtered text: {{ filterText }}</p>
    
    <p >
      Showing
      {{ start }}
      from
      {{ totalPoke }}
    </p>
    <div class="row m-2 " >
        <PokeCard 
          v-for="(pokes, index) in pokemonStore.filteredList"
          :key="`poke-${index}`"
          :pokeName="pokes.name"
          :pokeUrl="pokes.url"
          @getName="getPokeName"
        />
    </div>
    <select
      class="form-select"
      v-model="currentPage"
      @change="pageNav(currentPage)"
    >
      <option v-for="index in totalPage" :key="index">
        {{ index }}
      </option>
    </select>
    <nav aria-label="Page navigation example">
      <ul class="pagination p-2">
        <li class="page-item">
          <a
            class="page-link"
            @click="pageNav('prev')"
            v-if="pokemonStore.list.length <= start"
            >Previous</a
          >
        </li>
        <li class="page-item" v-for="index in prevLink" :key="index" @click="pageNav( currentPage - index)">
          <a class="page-link" v-if="currentPage - index > 0">
            {{ currentPage - index }}
          </a>
        </li>
        <li class="page-item active" :current="currentPage">
          <a class="page-link" v-if="currentPage == 0">
            {{ currentPage + 1 }}
          </a>
          <a class="page-link" v-else>{{ currentPage }}</a>
        </li>
        <li class="page-item" v-for="index in nextLink" :key="index" @click="pageNav(currentPage + index)">
          <a class="page-link" v-if="index > 1 && currentPage < totalPage">
            {{ currentPage + index }}
          </a>
        </li>
        <li class="page-item">
          <a class="page-link"
            @click="pageNav('next')"
            v-if="currentPage < totalPage"> Next</a
          >
        </li>
      </ul>
    </nav>
  </div>
</template>

