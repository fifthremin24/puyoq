<template>
  <v-app>
    <v-content>
      <AppMain/>
    </v-content>
  </v-app>
</template>

<script>
import AppMain from './components/AppMain';

export default {
  name: 'App',

  components: {
    AppMain,
  },

  data: () => ({
    //
  }),
};
</script>
