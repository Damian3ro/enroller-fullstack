<template>
  <form @submit.prevent="addNewMeeting()">
    <h3>Dodaj nowe spotkanie</h3>
    <label>Nazwa</label>
    <input type="text" v-model="newMeeting.title">
    <label>Opis</label>
    <textarea v-model="newMeeting.description"></textarea>
    <button>Dodaj</button>
    <span class="error" v-if="missingTitleError">Spotkanie musi mieć nazwę!</span>
    <span class="error" v-if="duplicatedTitleError">Spotkanie o podanej nazwie istnieje. Wprowadź inną nazwę.</span>
  </form>
</template>

<script>
export default {
  props: {
    meetings: Array
  },
  data() {
    return {
      newMeeting: {participants: []},
      missingTitleError: false,
      duplicatedTitleError: false
    };
  },
  methods: {
    addNewMeeting() {
      var today = new Date();
      var dd = String(today.getDate()).padStart(2, '0');
      var mm = String(today.getMonth() + 1).padStart(2, '0');
      var yyyy = today.getFullYear();
      today = yyyy + '-' + mm + '-' + dd;

      this.newMeeting.date = today;

      this.duplicatedTitleError = false;
      this.missingTitleError = false;
      let meetingTitles = [];
      let indexMeeting = null;
      if(this.meetings != null) {
          this.meetings.forEach(element => {
            meetingTitles.push(element.title);
          });
          indexMeeting = meetingTitles.indexOf(this.newMeeting.title);
          this.missingTitleError = false;
          this.duplicatedTitleError = true;
      }

      console.log('indexMeeting = ' + indexMeeting);
      if (!this.newMeeting.title) {
        this.duplicatedTitleError = false;
        this.missingTitleError = true;
      };
      if (this.newMeeting.title && (indexMeeting == null || indexMeeting < 0)) {
        this.$emit('added', this.newMeeting);
        this.missingTitleError = false;
        this.duplicatedTitleError = false;
        this.newMeeting = {participants: []};
        console.log('newMeeting added: ' + this.newMeeting);
      };
    }
  }
}
</script>

<style scoped>
.error {
  padding-left: 30px;
  text-align: center;
  color: #F00;
}
</style>
