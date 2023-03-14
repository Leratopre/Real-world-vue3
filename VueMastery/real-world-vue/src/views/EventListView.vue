<script >
import {ref, onMounted, watchEffect } from 'vue'
import EventCard from '@/components/Eventcard.vue'
import axios from 'axios'
import EventServices from '@/services/EventService.js'

const events = ref(null)
onMounted(() => {
  axios.get('https://my-json-server.typicode.com/code-Pop/Real-world_Vue-3/events')
  .then((response) => {
    console.log('events:', response.data)
  })
  .catch((error) => {
    console.log(error)
  })
})

export default {
  name: 'EventListView',
  props: ['page'],  //receive the param as a prop, the current page
  components: {
    EventCard
  },
  data() {
    return {
      events: null,
      totalEvents: 0
    }
  },
  created() {
watchEffect(() => {
  this.events = null
    EventServices.getEvents(2, this.page)  // 2 events per page, and current page
    .then(response => {
      this.events = response.data
      this.totalEvents = response.headers['x-total-count']
})
.catch(error => {
Console.log(error)
})
})
  },
computed: {
  hasNextPage() {
    var totalPages = Math.ceil(this.totalEvents / 2)

  return this.page < totalPages
    }
  }
}
</script>


<template>
  <h1>Events for Good</h1>
    <div class="events">
      <EventCard v-for="event in events" :key="event.id" :event="event" />
  
      <div class="pagination">
      <router-link
      id="page-prev"
      :to="{ name: 'EventListView', query: { page: page - 1 } }"
      rel="prev"
      v-if="page != 1"
      >&#60; Previous</router-link>
  
      <router-link
      id="page-next"
      :to="{ name: 'EventListView', query: { page: page + 1 } }"
      rel="next"
      v-if="isNextPage"
      >Next &#62;</router-link>
        </div>
      </div>
  </template>


<style scoped>
.events {
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>
