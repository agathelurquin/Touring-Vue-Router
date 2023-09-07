<template>
  <p>Register to the Event</p>
  <button @click="register">Register Me</button>
</template>

<script>
export default {
  props: ['event'],
  methods: {
    register() {
      this.$router.push({
        name: 'About'
        // params populated by default from the parent root URL
      })
    }
  }
}
</script>
