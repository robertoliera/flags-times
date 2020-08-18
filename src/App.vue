<template>
  <div id="app">
    <header class="p-4 bg-green-400">
      <h1 class="text-6xl">
        🏴‍🕑Flags & Times
      </h1>
    </header>

    <main class="flex">
      <CountryList @change="converTimes" />

      <section class="flex-1 p-5">
        <label for="selectedDate"></label>
        <input
          id="selectedDate"
          type="datetime-local"
          v-model="state.selectedDate"
          placeholder="Seleccionar fecha"
        />
        <div v-html="state.message"></div>
      </section>
    </main>
  </div>
</template>

<script>
import { reactive, watch } from "vue";
import { utcToZonedTime } from "date-fns-tz";

import emojis from "@/emojis";

import CountryList from "@/components/CountryList";

export default {
  name: "App",

  components: {
    CountryList,
  },

  setup() {
    const state = reactive({
      convertedCountries: [],
      selectedDate: new Date(),
      message: "",
    });

    let localCountries = [];

    function converTimes(countries) {
      if (!countries.length) {
        return false;
      }

      const now = state.selectedDate;
      let message = "";

      localCountries = countries;

      countries.forEach((country) => {
        const dates = Array.from(
          new Set(
            country.timezones.map((tz) =>
              utcToZonedTime(now, tz).toLocaleString()
            )
          )
        );

        let flag = emojis["flag-" + country.id.toLowerCase()].b
          .split("-")
          .map((code) => `&#x${code};`)
          .join();

        flag = `<span role="img">${flag}</span>`;

        message += `${flag} ${country.name}: ${dates.join("\n")}`;
      });

      state.message = message;
    }

    watch(
      () => state.selectedDate,
      () => {
        converTimes(localCountries);
      }
    );

    return { state, converTimes };
  },
};
</script>

<style></style>
