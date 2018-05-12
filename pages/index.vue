<template>
  <v-layout>
    <v-flex xs12>
      <Mynavigation></Mynavigation>
    </v-flex>
  </v-layout>
</template>

<script>
import Mynavigation from '../components/Navigation'
export default {
  middleware: ['auth'],
  components: {
    Mynavigation
  },
  data () {
    return {
    }
  }
}
</script> 