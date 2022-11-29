<template>
  <v-app>
    <!-- <v-app-bar dense app color="grey darken-4">
      <v-app-bar-title> Stanton </v-app-bar-title>
      <v-spacer></v-spacer>
      <v-col>
        <v-slider
          class="mt-5"
          hint="Im a hint"
          max="50"
          min="-50"
          label="Orbit Speed"
        ></v-slider>
      </v-col>
      <v-col>
        <v-row>
          <v-switch
            class="mt-4 pl-2"
            dense
            inset
            v-model="orbit"
            label="Grid"
          ></v-switch>
          <v-switch
            class="mt-4 pl-3"
            dense
            inset
            v-model="orbit"
            label="Orbit"
          ></v-switch>
        </v-row>
      </v-col>
    </v-app-bar> -->
    <v-main>
      <HelloWorld />
    </v-main>
  </v-app>
</template>

<script>
import HelloWorld from "./components/HelloWorld";

export default {
  name: "App",

  components: {
    HelloWorld,
  },

  data: () => ({
    //
  }),
};
</script>
