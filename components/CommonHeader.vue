<template>
  <header>
    <h1 class="title"><NuxtLink to="/"><img src="/logo.png" alt="画像" width="100px"></NuxtLink></h1>
    <nav class="nav">
      <ul class="menu-group">
        <li class="menu-item" v-if="!loginCheck">
          <NuxtLink to="/register">新規登録</NuxtLink>
        </li>
        <li class="menu-item" v-if="!loginCheck">
          <NuxtLink to="/login">ログイン</NuxtLink>
        </li>
        <li class="menu-item" v-else>
          <a @click="logout">ログアウト</a>
        </li>
      </ul>
    </nav>
  </header>
</template>

<script>
import firebase from '~/plugins/firebase'
export default {
  data() {
    return {
      loginCheck: false,
    }
  },
  created() {
    firebase.auth().onAuthStateChanged((user) => {
      if (user) {
        this.loginCheck = true
      }
    })
  },
  methods: {
    logout() {
      firebase
        .auth()
        .signOut()
        .then(() => {
          alert('ログアウトが完了しました')
          this.loginCheck = false
          this.$router.replace('/')
        })
    },
  },
};
</script>

<style scoped>

header {
  display: flex;
  height: 50px;
  padding: 0 10px;
  margin-bottom: 10px;
  background-color: rgb(31, 33, 42);
  align-items: center;
}
.title {
  margin-right: auto;
}
.menu-item {
  list-style: none;
  display: inline-block;
  padding: 10px;
}
.menu-item a {
  color: rgb(255, 255, 255);
  text-decoration: none;
  cursor: pointer;
}
</style>