<template>
  <div>
    <v-snackbar
      :timeout="playedmessage.timeout"
      :color="playedmessage.color"
      :multi-line="playedmessage.mode === 'multi-line'"
      :vertical="playedmessage.mode === 'vertical'"
      v-model="playedmessage.snackbar"
      >
      {{ playedmessage.text }}
      <v-btn dark flat @click.native="playedmessage.snackbar = false">Fermer</v-btn>
    </v-snackbar>
  </div>
</template>
<script>
export default {
  name: 'Mysnackbar',
  props: ['playedmessage'],
  data () {
    return {

    }
  }
}
</script>