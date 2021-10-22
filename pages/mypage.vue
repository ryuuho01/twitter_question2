<template>
  <div>
    <div v-if="!loginCheck" class="homecontainer">
      <NuxtLink to="/"><img class="homelogo" src="/logo.png" alt="画像" width="100px"></NuxtLink>
      <p class="menu-title">掲示板型SNSサービス</p>
      <div class="window">
        <NuxtLink class="menu" to="/register"><p>新規登録</p></NuxtLink>
        <br />
        <NuxtLink class="menu" to="/login"><p>ログイン</p></NuxtLink>
        <br />
        <NuxtLink class="menu" to="/mypage"><p>マイページへ</p></NuxtLink>
      </div>
    </div>
<!-- -------------------------左　　　側--------------------------- -->
    <div v-else class="flex">
      <div class="container-left">
        <p><NuxtLink class="homettl" to="/">　ホーム</NuxtLink></p><br>
        <a class="logoutttl" @click="logout">　ログアウト</a>
        <div class="share">
          <p>シェア</p><br>

          <textarea v-model="newPost" name="post" cols="30" rows="7" id="share"></textarea><br>
          <button @click="insertPost" class="share-button">シェアする</button>
        </div>
      </div>
<!-- -------------------------右　　　側--------------------------- -->
      <div class="container-right">
        <div class="description">
          <p>ホーム</p>
          <!-- <button @click="update" class="share-button">更新する</button> -->
        </div>
        <div class="postdescription" v-for="item in postCurrent" :key="item.id">
          <p>{{item.name}}</p>
          <form action="http://127.0.0.1:8000/api/like/update" method="PUT">
            <input type="hidden" :value="item.id" name="id">

            <!-- <button @click="insertLike">Like</button> -->
            <button>Like</button>
          </form>
          <p>{{item.post}}</p><br>
        </div>
        <div class="postdescription" v-for="item in postLists" :key="item.id">
          <p>{{item.name}}</p><br>
          <p>{{item.post}}</p><br>
        </div>
      </div>

    </div>

  </div>

</template>

<script>
import firebase from '~/plugins/firebase'
import axios from "axios";
export default {
  data() {
    return {
      clientId: "",
      loginCheck: false,
      newPost: "",
      postId: "",
      postCurrent: [],
      postLists: [],
      updatePostId: "",
      // likeId: [],
      // Likes: [],
    }
  },
  mounted() {
    axios
      .get("http://127.0.0.1:8000/api/post/")
      .then((response) => (this.postCurrent = response.data.data));
  },
  // -----------↓FireBase↓----------
  created() {
    firebase.auth().onAuthStateChanged((user) => {
      // if (user) {
        this.loginCheck = true;
        // axios
        // .get("http://127.0.0.1:8000/api/client/")
        // .then(function(response){
        // const userinfo = firebase.auth().currentUser;
        // var Id = 5;
        // const email = userinfo.email;
        // console.log(email);
        // console.log(response.data.data);
        // for(let i =0; i < response.data.data.length; i++) {
        //   if(response.data.data[i]["email"] == email) {
        //     Id = response.data.data[i]["id"];
        //     break
        //   }
        // };
        // console.log(Id);
        // this.clientId = Id;
        // });
      // }
    })
    console.log(this.loginCheck);
    this.loginCheck = 1;
    console.log(this.loginCheck);
  },
  // -----------↑FireBase↑----------
  methods: {
    // -----------↓FireBase↓----------
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
    // -----------↑FireBase↑----------

    async getPost() {
      const postresData = await this.$axios.get("http://127.0.0.1:8000/api/post/");
      this.postLists.push(postresData.data.data.slice(-1)[0]);
      this.postId = postresData.data.data.slice(-1)[0].id;
      const sendlikeData = {
          client_id: this.clientId,
          post_id: this.postId,
          like: 0,
      }
      await this.$axios.post("http://127.0.0.1:8000/api/like/", sendlikeData);
    },

    async insertPost() {
      // const user = firebase.auth().currentUser;
      // if(user !== null) {
      //   let Id = 0;
      //   const email = user.email;
      //   const clientresData = await this.$axios.get("http://127.0.0.1:8000/api/client/");
      //   for(let i =0; i < clientresData.data.data.length; i++) {
      //     if(clientresData.data.data[i]["email"] == email) {
      //       Id = clientresData.data.data[i]["id"];
      //     };
      //   }
      //   this.clientId = Id;
        // console.log(clientresData.data.data[0]["email"]);
        // console.log(this.clientId);
        const sendpostData = {
          post: this.newPost,
          client_id: this.clientId,
        };
        await this.$axios.post("http://127.0.0.1:8000/api/post/", sendpostData);

        this.newPost = '';
        this.getPost();
      // };
    },
    async insertLike() {

      // if(user !== null) {
        const postresData = await this.$axios.get("http://127.0.0.1:8000/api/post/");
        this.postId = postresData.data.data.slice(-1)[0].id;

      const sendlikeData = {
          like: 1,
        };
      await this.$axios.put("http://127.0.0.1:8000/api/like/"+clientId, sendlikeData);
      // };
    },
  },
}
</script>

<style>
.flex {
  display: flex;
}
.container-left {
  color: white;
  margin-right: 20px;
}
.container-right {
  color: white;
  width: 100%;
  margin-right: 10px;
}
.homettl {
  text-decoration: none;
  color: white;
}
.homettl::before {
  margin-left: 10px;
  content: "";
  background: url(/home.png);
  background-size: cover;
  vertical-align: bottom;
  height: 20px;
  width: 20px;
  display: inline-block;
}
.logoutttl {
  cursor: pointer;
}
.logoutttl::before {
  margin-left: 10px;
  content: "";
  background: url(/logout.png);
  background-size: cover;
  vertical-align: bottom;
  height: 20px;
  width: 20px;
  display: inline-block;
}

.share {
  padding: 30px 0 0 10px;
  position: relative;
}

textarea {
  resize:none;
  background: rgb(31, 33, 42);
  border-radius: 10px;
  color: white;
}

.share-button {
  margin-top: 20px;
  border: none;
  position: absolute;
  right: 0%;
}

.description {
  border: 1px solid rgb(129, 129, 129);
  padding: 15px 10px;
}

.postdescription {
  border: 1px solid rgb(129, 129, 129);
  padding: 15px 10px;
  border-top: none;
}

li {
  list-style-type: none;
}

</style>