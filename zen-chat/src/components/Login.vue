
<template>
  <div class="mt-4">
    <h2>ZenClass Chat App</h2>
    <form class="detail-box my-5">
      <div class="form-group my-3">
        <h4>войти в приложении</h4>
        <input
          type="text"
          class="form-control mb-4 mt-4"
          v-model="name"
          placeholder="введите имя..."
          v-on:keyup.enter="login"
        />

        <button
          type="button"
          v-on:click="login"
          class="btn btn-primary"
          style="font-weight: 600"
        >
          логин
        </button>
      </div>
    </form>
    <h3>имена пользователей</h3>
    <div v-for="user in usersList" :key="user.id">
      <p>{{ user.name }}</p>
    </div>
  </div>
</template>


<script>
import axios from "axios";
import Vue from "vue";
export default {
  app: "Login",
  data() {
    return {
      usersList: [],
      photosList: [],
      msgList: [],
      chatPersonNames: [],
      name: "",
    };
  },
  mounted() {
    const getUsers = "https://jsonplaceholder.typicode.com/users/";
    const getPhotos = "https://jsonplaceholder.typicode.com/photos/";

    axios
      .get(getUsers)
      .then((response) => {
        this.usersList = [...response.data].slice(0, 10);
      })
      .catch((err) => {
        console.log(err);
      });
    axios
      .get(getPhotos)
      .then((response) => {
        this.photosList = [...response.data].slice(0, 10);
      })
      .catch((err) => {
        console.log(err);
      });
  },
  methods: {
    login() {
      const name = this.name;
      this.usersList.map((user) => {
        this.chatPersonNames.push(user.name);
      });

      if (name == "") {
        Vue.toasted.show("введите имя плиз").goAway(2000);
        return;
      }

      if (!this.chatPersonNames.includes(name)) {
        Vue.toasted.show("только эти имена ниже").goAway(2000);
        return;
      }

      for (let index = 0; index < this.usersList.length; index++) {
        const chatter = this.usersList[index];
        const chatterPhoto = this.photosList[index];
        if (chatter.name == this.name) {
          localStorage.setItem("id", chatter.id);
          localStorage.setItem("name", chatter.name);
          localStorage.setItem("photoURL", chatterPhoto.url);
        }
      }

      this.$router.push("/chat");
    },
  },
};
</script>

<style scoped>
input[type="text"],
input[type="password"] {
  margin: 0 auto;
  width: 80%;
  border: 1px solid lightgrey;
}
.detail-box {
  padding: 5px;
  border: 1px solid lightgrey;
  width: 400px;
  min-height: 250px;
  margin: 0 auto;
}
h2,
h4 {
  color: dodgerblue;
  font-weight: 600;
}
</style>




