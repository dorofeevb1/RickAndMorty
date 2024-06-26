<template>
  <div
    class="flex place-content-center"
  >
    <a
      href=""
      class="text-xl flex relative"
      :class="{ 'filters-icon-anim': animateIcon }"
      @click.prevent="openCloseFilters = !openCloseFilters"
    >
      <i
        class="fa-solid fa-filter hover:text-green-200 transition-colors duration-100 ease-in-out"
        :class="[ openCloseFilters ? 'text-green-300' : 'text-gray-300']"
      />
      <p
        v-if="Object.keys(storeCharacters.requestFilters).length > 1"
        class="absolute"
        style="top: -1px; right: -1px"
      >
        <span class="block bg-red-500 w-2 h-2 rounded-2xl" />
      </p>
    </a>
  </div>

  <div
    class="filters-wrapper flex flex-col items-center transition-all duration-300 ease-in-out"
    :class="{ 'h-0': openCloseFilters, 'overflow-hidden': openCloseFilters}"
  >
    <input
      id="characterName"
      v-model="selectedFilters.name"
      placeholder="Поиск по имени..."
      type="text"
      class="w-2/5 min-w-max h-8 mt-6 rounded-2xl border border-gray-300 text-center placeholder:text-gray-300 drop-shadow-sm focus:border-green-300 focus:ring-5 focus:ring-green-200 outline-none transition-colors duration-200 ease-in-out"
      @input="setFilter('name', selectedFilters.name)"
    >
    <div
      class="dropdown-wrapper max-w-xl grid grid-cols-2 gap-x-10 gap-y-1 mt-6"
    >
      <label
        v-for="filter in filterCategories"
        :key="filter.id"
        for="dropdown"
        class="flex"
      >
        <span class="capitalize text-green-500 mr-2">
          {{ filter.name }}:
        </span>
        <select
          id="filters"
          v-model="selectedFilters[filter.name]"
          class="capitalize outline-0 w-full text-center text-ellipsis"
          name="filters"
          @change="setFilter(filter.name, selectedFilters[filter.name])"
        >
          <option selected value="all">
            -
          </option>
          <option
            v-for="subFilter in filter.subFilters"
            :key="subFilter"
            :value="subFilter"
          >
            {{ subFilter }}
          </option>
        </select>
      </label>
    </div>

    <a
      v-if="Object.keys(storeCharacters.requestFilters).length > 1"
      href=""
      class="mt-4 underline text-red-400 hover:text-red-300 transition-colors duration-100 ease-in-out"
      @click.prevent="resetFilters"
    >
      clear filters
    </a>
  </div>

  <div
    class="mt-6 flex place-content-center"
  >
    <p class="ml-1">
      {{ idRange()[0] }} - {{ idRange()[1] }}
    </p>
    <p class="ml-2">of</p>
    <p class="text-green-400 ml-2">
      {{ storeCharacters.charactersCount }}
    </p>
  </div>
</template>

<script setup>
/* 
  imports
*/

  import { ref } from 'vue'
  import { useStoreCharacters } from '@/stores/storeCharacters'

/* 
  stores
*/

  const storeCharacters = useStoreCharacters()

/*
  open close filters
*/

  const openCloseFilters = ref(true)

/* 
  characters id range
*/

  const idRange = () => {
    const currentPage = storeCharacters.requestFilters.page
    const charactersCount = storeCharacters.charactersCount
    const charactersLength = storeCharacters.characterItems.length

    if(charactersLength === 0) {
      return [0, 0]
    }

    return [
      ((currentPage - 1) * 20) + 1,
      currentPage * 20 > charactersCount ? charactersCount : currentPage * 20,
    ]
  }

/* 
  get filters
*/

  const filterCategories = storeCharacters.filterCategories

/* 
  selected filters default
*/

  const selectedFilters = ref({
    name: '',
    status: 'all',
    species: 'all',
    type: 'all',
    gender: 'all'
  })

/* 
  update selected filters from session storage
*/

  Object.keys(selectedFilters.value).forEach(el => {
        if(storeCharacters.requestFilters[el]) {
          selectedFilters.value[el] = storeCharacters.requestFilters[el]
        }
      })
  
/* 
  reset filters
*/
  const resetFilters = () => {
    selectedFilters.value.name = ''
    selectedFilters.value.status = 'all'
    selectedFilters.value.species = 'all'
    selectedFilters.value.type = 'all'
    selectedFilters.value.gender = 'all'
    storeCharacters.resetStoreFilters()
  }


/* 
  call filters request method in store
*/

  const setFilter = (filterName, subFilter) => {
    storeCharacters.setStoreFilters(filterName, subFilter)
  }

/*
  filters icon animation handling
*/

let animateIcon = ref(true)

if(!sessionStorage.getItem('iconAnimated')) {
  sessionStorage.setItem('iconAnimated', true)
} else {
  animateIcon.value = false
}


</script>

<style scoped>
  .filters-icon-anim {
    animation: flash;
    animation-duration: 1.6s;
  }
  /* select {
    -webkit-appearance:caret;
  } */
</style>