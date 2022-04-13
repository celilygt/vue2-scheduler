<template>
    <v-main>
      <weekly-schedule/>
    </v-main>
</template>

<script>
import WeeklySchedule from './components/WeeklySchedule.vue';

export default {
  name: 'App',

  components: {
    WeeklySchedule,
  },

  data: () => ({
    //
  }),
};
</script>
