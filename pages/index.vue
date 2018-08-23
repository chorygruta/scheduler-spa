<template>
  <v-container fluid fill-height class="page-container">
      <v-layout wrap justify-center align-center>
        <v-flex xs12 sm8 md6 class="text-xs-center">
          <v-btn
            class="schedule-btn" 
            large
            color="primary"
            @click="activator = !activator"
          >
            Schedule Overview Call
          </v-btn>
        </v-flex>
        <v-flex xs12>
          <schedule-card
            v-on:cancelScheduler="activator = false"
            v-on:successAction="successAction"
            v-on:errorAction="errorAction"
            :activator = activator
            modal
          ></schedule-card>
        </v-flex>
      </v-layout>
  </v-container>
</template>

<script>
import ScheduleCard from './../components/schedule-card'

export default {
  components: {
    ScheduleCard
  },
  data () {
    return {
      activator: false
    }
  },
  methods: {
    successAction () {
      this.activator = false
      alert('Call Scheduled. You will receive a calendar invite.')
    }, 
    errorAction () {
      this.activator = false
      alert('Error occured. Unable to schedule call.')
    }
  },
}
</script>

<style scoped>
  .page-container {
    background-size: cover;
    background-position: 50%;
    text-align: center;
  }
  .schedule-btn {
    background-color:#25bb87;
    border-radius: 3px;
    text-transform: capitalize;
  }
</style>