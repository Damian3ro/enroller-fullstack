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
      this.authenticatedUsername = user.login;
    },
    logMeOut() {
      this.authenticatedUsername = '';
    },
    register(user) {
      axios.post('/api/participants', user)
          .then(response => {
            // udało się
            this.userSignedUp = true;
            this.message = 'Udało się założyć konto.';
          })
          .catch(response => {
            // nie udało sie
            this.userSignedUp = false;
            this.message = 'Nie udało się założyć konta.';
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