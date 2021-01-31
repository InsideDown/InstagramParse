<template>
  <div class="container">
    <button type="button" @click="getInstagramPermissions()" v-if="instagramAccessToken === ''">
      Get my Instagram feed
    </button>
    <div class="instagram feed">
      <div
        v-for="(curMedia,index) in mediaData"
        :key="index"
        class="grid-container"
      >

        <div 
          v-if="curMedia.thumbnail_url" 
          :style="{ backgroundImage: 'url(' + curMedia.thumbnail_url + ')' }"

          class="grid-item">
          
        </div>
        <div 
          v-else
          :style="{ backgroundImage: 'url(' + curMedia.media_url + ')' }"
          class="grid-item">
          
        </div>
      </div>
    </div>
  </div>
</template>

<script>

const FormData = require('form-data');

export default {
  name: 'Instagram',
  props: {
    msg: String
  }, 
  data() {
    return {
      clientID: "",
      clientSecret:"",
      scope: "user_profile,user_media",
      redirectURL: "https://localhost:8080/",//"https://insidedown.com/instagram/",
      instagramCode:"",
      instagramAccessToken:"",
      instagramUserID:"",
      mediaData: null
    }
  },
  methods: {
    getInstagramPermissions() {
      let urlStr = "https://api.instagram.com/oauth/authorize?client_id=" +
      this.clientID + 
      "&redirect_uri=" + this.redirectURL +
      "&scope=" + this.scope + 
      "&response_type=code";
      window.open(urlStr, "_self");
    }, 
    checkURLForCode() {
      const queryString = window.location.search;
      const urlParams = new URLSearchParams(queryString);
      const codeParam = urlParams.get('code');
      //if we have a code but we don't have an access token yet
      if(codeParam && this.instagramAccessToken == "") {
        this.instagramCode = codeParam;
        this.getAccessToken();
      }
    },
    async getUserPhotos() {
      if(this.instagramAccessToken !== "" && this.instagramUserID !==""){

        let instagramURL = "https://graph.instagram.com/" 
        + "me/" 
        + "media?fields=media_type,media_url,thumbnail_url&" 
        + "access_token=" + this.instagramAccessToken;
        fetch(instagramURL)
        .then(response => response.json())
        .then(data => {
          this.mediaData = new Array();
          this.mediaData = data.data;
          this.displayMedia();
        });
      }
    },
    displayMedia() {
      for(let i=0;i<this.mediaData.length;i++) {
        let curMedia = this.mediaData[i];
        if(curMedia) {
          //console.log(curMedia.thumbnail_url);
        }   
      }
    },

    async getAccessToken() {
      const insta_form = new FormData();
      insta_form.append("client_id", this.clientID);
      insta_form.append("client_secret", this.clientSecret);
      insta_form.append("grant_type", "authorization_code");
      insta_form.append("redirect_uri", this.redirectURL);
      insta_form.append("code", this.instagramCode);
      const shortTokenRes = await fetch(
        "https://api.instagram.com/oauth/access_token",
        {
          method: "POST",
          body: insta_form,
        }
      );
      const results = await shortTokenRes.json();
      if(results) {
        if(results.user_id){
          this.instagramAccessToken = results.access_token;
          this.instagramUserID = results.user_id;
          this.getUserPhotos();
        }
      }
    }
  },
  mounted() {
    this.clientID = process.env.VUE_APP_CLIENT_ID;
    this.clientSecret = process.env.VUE_APP_CLIENT_SECRET;
    this.checkURLForCode();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

.grid-container {
  display: inline-grid;
  grid-template-columns: auto auto auto;
  padding: 10px;
}
.grid-item {
  background-size: cover;
  width: 200px;
  height: 200px;
}
</style>
