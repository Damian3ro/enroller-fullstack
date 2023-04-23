<template>
  <div id="app">
    <h1>Witaj w systemie do zapisów na zajęcia</h1>

    <div v-if="authenticatedUsername">
      <UserPanel :username="authenticatedUsername" @logout="logMeOut()"></UserPanel>
      <MeetingsPage :username="authenticatedUsername"></MeetingsPage>
    </div>

    <div v-else>
      <button :class="signingUp ? 'button-outline' : ''" @click="signingUp = false">Logowanie</button>
      <button :class="!signingUp ? 'button-outline' : ''" @click="signingUp = true">Rejestracja</button>

      <LoginForm v-if="!signingUp" @login="(user) => logMeIn(user)"></LoginForm>
      <LoginForm v-else @login="(user) => register(user)" button-label="Załóż konto"></LoginForm>
      <div v-if="message" :class="userSignedUp ? 'greenAlert' : 'redAlert'">
        {{ message }}
      </div>
    </div>
  </div>
</template>

<script>
import "milligram";
import LoginForm from "./LoginForm";
import UserPanel from "./UserPanel";
import MeetingsPage from "./meetings/MeetingsPage";
import axios from "axios";

export default {
  components: {LoginForm, MeetingsPage, UserPanel},
  data() {
    return {
      signingUp: false,
      authenticatedUsername: '',
      userSignedUp: false,
      message: ''
    }
  },
  methods: {
    logMeIn(user) {
      axios.post('/api/tokens', user)
          .then(response => {
            this.authenticatedUsername = user.login;
            const token = response.data.token; //uzyskanie tokena
            //Podanie tokena do autoryzacji:
            axios.defaults.headers.common['Authorization'] = 'Bearer ' + token;
            axios.get('/api/meetings').then(response => console.log(response.data));
          })
          .catch(response => {
            this.userSignedUp = false;
            this.message = 'Logowanie nieudane';
          });
    },
    logMeOut() {
      this.authenticatedUsername = '';
      delete axios.defaults.headers.common.Authorization;
    },
    register(user) {
      axios.post('/api/participants', user)
          .then(response => {
            this.userSignedUp = true;
            this.message = 'Udało się założyć konto :)';
          })
          .catch(response => {
            this.userSignedUp = false;
            this.message = 'Nie udało się założyć konta :(';
          });
    }
  }
}
</script>

<style>
.greenAlert {
  padding: 5px;
  margin: 10px;
  color: green;
  text-align: center;
  font-size: 14pt;
}

.redAlert {
  padding: 5px;
  margin: 10px;
  color: red;
  text-align: center;
  font-size: 14pt;
}
</style>