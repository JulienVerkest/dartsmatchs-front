<template>
  <v-layout>
    <v-flex>
      <Mynavigation></Mynavigation>
      <div class="mt-5">
        <v-card v-if="$auth.loggedIn">
          <v-alert type="error" :value="error">{{error}}</v-alert>
          <v-card-text>
            Vous êtes actuellement connecté en tant que {{$auth.state.user.email}}
          </v-card-text>
          <v-card-actions>
            <v-btn @click="logout">Me déconnecter</v-btn>
          </v-card-actions>
        </v-card>
        <v-card v-else>
          <v-alert type="error" :value="error">{{error}}</v-alert>
          <v-card-text>
            <v-form>
              <v-text-field v-model="email" label="Email" />
              <v-text-field v-model="password" label="Mot de passe" type="password" />
            </v-form>
            <v-card-actions>
              <v-btn @click="login">Me connecter</v-btn>
            </v-card-actions>
          </v-card-text>
        </v-card>
      </div>
    </v-flex>
  </v-layout>
</template>

<script>
import Mynavigation from '../components/Navigation'
export default {
  components: {
    Mynavigation
  },
  data () {
    return {
      email: '',
      password: '',
      error: null
    }
  },
  methods: {
    login: function () {
      console.log(this.$auth)
      // console.log(this.$auth.strategies.endpoints)
      // this.$axios.post('/api/users/sign_in', {
      this.$auth.loginWith('local', {
        data: {
          user: {
            email: this.email,
            password: this.password
          }
        }
      }).catch(e => {this.error = e + ''})
    },
    logout: function () {
      this.$auth.logout().catch(e => {this.error = e + ''})
    }
  }
}
</script>