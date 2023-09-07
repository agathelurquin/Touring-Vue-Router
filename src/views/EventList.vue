<template>
  <h1>Events for Good</h1>
  <div class="events">
    <EventCard v-for="event in events" :key="event.id" :event="event" />
    <div class="pagination">
      <router-link
        :to="{ name: 'EventList', query: { page: page - 1 } }"
        rel="prev"
        id="page-prev"
        v-if="page != 1"
        >&#60;Prev Page</router-link
      >
      <router-link
        :to="{ name: 'EventList', query: { page: page + 1 } }"
        rel="next"
        id="page-next"
        v-if="hasNextPage"
        >&#62;Next Page</router-link
      >
    </div>
  </div>
</template>

<script>
import EventCard from '@/components/EventCard.vue'
import EventService from '@/services/EventService.js'
import NProgress from 'nprogress'

export default {
  name: 'EventList',
  props: ['page'],
  components: {
    EventCard
  },
  data() {
    return {
      events: null,
      totalEvents: 0
    }
  },

  beforeRouteEnter(routeTo, routeFrom, next) {
    NProgress.start()
    return EventService.getEvents(2, parseInt(routeTo.query.page) || 1)
      .then(response => {
        next(comp => {
          comp.events = response.data
          comp.totalEvents = response.headers['x-total-count']
        })
      })
      .catch(() => {
        next({ name: 'NetworkError' })
      })
      .finally(() => {
        NProgress.done()
      })
  },
  beforeRouteUpdate(routeTo) {
    NProgress.start()
    return EventService.getEvents(2, parseInt(routeTo.query.page) || 1)
      .then(response => {
        this.events = response.data
        this.totalEvents = response.headers['x-total-count']
      })
      .catch(() => {
        return { name: 'NetworkError' }
      })
      .finally(() => {
        NProgress.done()
      })
  },
  computed: {
    hasNextPage() {
      const totalPages = Math.ceil(this.totalEvents / 2)
      return this.page < totalPages
    }
  }
}
</script>

<style scoped></style>
