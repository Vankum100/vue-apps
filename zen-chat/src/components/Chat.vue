<template>
  <div class="wrapper">
    <!-- Sidebar  -->
    <nav style="color: white" id="sidebar">
      <div class="sidebar-header d-flex justify-content-around">
        <div class="d-flex pointer" style="margin-right: 10px; width: 180px">
          <img
            :src="photoURL"
            alt="user"
            width="48px"
            height="48px"
            style="border-radius: 50%"
          />
        </div>
        <button type="button" class="btn btn-primary" v-on:click="logout">
          Выйти
        </button>
      </div>
      <div style="height: 1px; border-bottom: 1px solid #00388b"></div>
      <ul class="list-unstyled components">
        <li
          class="active mb-3"
          v-on:click="letsChat(item)"
          v-for="item in chatMembers"
          :key="item.id"
          v-show="item.id != currentUserId"
        >
          <div
            class="d-flex"
            style="cursor: pointer; padding-bottom: 15px; width: 100%"
          >
            <div style="width: 30%">
              <img
                :src="item.URL"
                alt="user"
                width="50px"
                height="50px"
                style="border-radius: 50%; background: white"
              />
            </div>
            <div
              style="
                padding: 10px 0px 0px;
                width: 50%;
                display: flex;
                justify-content: space-between;
              "
            >
              <h6 style="line-height: 2; font-weight: 540">{{ item.name }}</h6>
            </div>
          </div>
          <div style="height: 1px; border-bottom: 1px solid #00388b"></div>
        </li>
      </ul>
    </nav>

    <!-- Page Content  -->
    <div id="content" v-if="currentPeerUser === null">
      <div class="my-4">
        <img :src="photoURL" width="180px" class="br-50" />
      </div>
      <div>
        <h2>Привет {{ currentUserName }},</h2>
        <hr />
        <button
          type="button"
          class="btn btn-primary"
          v-on:click.once="getPartner"
        >
          кто в сети
        </button>
      </div>
    </div>
    <div v-else class="header-width">
      <ChatBox v-bind:currentPeerUser="currentPeerUser" />
    </div>
  </div>
</template>

<script>
import axios from "axios";

import ChatBox from "./ChatBox";
export default {
  app: "Chat",
  components: {
    ChatBox,
  },
  data() {
    return {
      currentUserName: localStorage.getItem("name"),
      currentPeerUser: null,
      currentUserId: localStorage.getItem("id"),
      currentUserPhoto: localStorage.getItem("photoURL"),
      chatMembers: [],
      photoURL: localStorage.getItem("photoURL"),
      users: [],
      photos: [],
      msgList: [],
    };
  },
  mounted() {
    const getUsers = "https://jsonplaceholder.typicode.com/users/";
    const getPhotos = "https://jsonplaceholder.typicode.com/photos/";
    const getMessages = "https://jsonplaceholder.typicode.com/comments/";

    axios
      .get(getUsers)
      .then((response) => {
        this.users = [...response.data].slice(0, 10);
      })
      .catch((err) => {
        console.log(err);
      });
    axios
      .get(getPhotos)
      .then((response) => {
        this.photos = [...response.data].slice(0, 10);
      })
      .catch((err) => {
        console.log(err);
      });
    axios
      .get(getMessages)
      .then((response) => {
        this.msgList = [...response.data].slice(0, 5);
      })
      .catch((err) => {
        console.log(err);
      });
  },
  methods: {
    letsChat(item) {
      this.currentPeerUser = item;
    },
    logout() {
      this.$router.push("/");
      localStorage.clear();
    },
    getPartner() {
      for (let index = 0; index < this.users.length; index++) {
        const chatter = this.users[index];
        const chatterPhoto = this.photos[index];
        this.chatMembers.push({
          id: chatter.id,
          name: chatter.name,
          URL: chatterPhoto.url,
        });
      }
    },
  },
  created() {
    if (!localStorage.hasOwnProperty("id")) this.$router.push("/");
    this.getPartner();
  },
};
</script>

<style scoped>
.pointer {
  cursor: pointer;
}
.br-50 {
  border-radius: 50%;
}
.header-width {
  width: calc(100% - 350px);
  min-height: 100vh;
  transition: all 0.3s;
  position: absolute;
  top: 0;
  right: 0;
}
</style>
