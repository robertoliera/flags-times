<template>
  <section class="flex-1 p-5">
    <input
      class="bg-white focus:outline-none focus:shadow-outline border border-gray-300 rounded-lg py-2 px-4 block appearance-none leading-normal"
      aria-label="Filtrar paises"
      v-model="state.filter"
      placeholder="Filtrar"
    />
    <ul>
      <li
        class="text-3xl"
        v-for="country in filteredCountries"
        :key="country.id"
      >
        <input v-model="country.selected" type="checkbox" :id="country.id" />
        <label :for="country.id">{{ country.name }}</label>
      </li>
    </ul>
  </section>
</template>

<script>
import { reactive, computed, watch } from "vue";
import ct from "countries-and-timezones";
export default {
  name: "CountryList",

  setup(props, context) {
    const state = reactive({
      countries: [],
      filter: "",
    });

    state.countries = Object.values(ct.getAllCountries()).map((country) => ({
      ...country,
      selected: false,
    }));

    const filteredCountries = computed(() => {
      if (!state.filter) {
        return state.countries;
      }

      return state.countries.filter((country) =>
        country.name.toLowerCase().includes(state.filter.toLowerCase())
      );
    });

    const selectedCountries = computed(() => {
      return state.countries.filter((country) => country.selected);
    });

    watch(selectedCountries, () => {
      context.emit("change", selectedCountries.value);
    });

    return { state, filteredCountries };
  },
};
</script>
