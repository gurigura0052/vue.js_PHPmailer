<template>
  <div class="container">
    <h2>{{ msg }}</h2>

    <mail></mail>
  </div>
</template>

<script>
  import Vue from 'vue'
  import mail from './mail/mail'

  Vue.component('mail', mail)

  export default {
    data () {
      return {
        msg: 'Home'
      }
    }
  }
</script>
