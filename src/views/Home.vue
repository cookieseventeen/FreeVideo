<template>
  <div class="home">
    <div class="container">
      <div class="row">
        <div class="col-md-12">
          <h1 class="mb-3 mt-3">請貼上你的播放清單</h1>
        </div>
      </div>
      <div class="row pt-5">
        <div class="col-md-12">
          <button
            type="button"
            class="btn btn-warning"
            :class="{ disabled: !loopMusic }"
            @click="setLoop"
          >
            重複播放
          </button>
          <button
            type="button"
            class="btn btn-info"
            :class="{ disabled: !randomMusic }"
            @click="setRandom"
          >
            隨機播放
          </button>
          <button type="button" class="btn btn-info" @click="setVidoe">
            下一首
          </button>
        </div>
        <!--
        <div class="col-md-12">
          <b-input-group class="mt-3">
            <template v-slot:append>
              <b-input-group-text><strong class="text-danger">請輸入單一影片</strong></b-input-group-text>
            </template>
            <b-form-input v-model="inputLink" ></b-form-input>
          </b-input-group>
        </div>
        -->
        <div class="col-md-12">
          <!-- Using components -->
          <b-input-group prepend="Youtube 列表" class="mt-3">
            <b-form-input v-model="playListLink"></b-form-input>
            <b-input-group-append>
              <b-button variant="info" @click="getListVideo"
                >載入播放列表</b-button
              >
            </b-input-group-append>
          </b-input-group>
        </div>
      </div>
      <div class="row">
        <div class="col-md-12" v-if="videoId">
          <youtube
            :video-id="videoId"
            ref="youtube"
            @playing="playing"
            :resize="true"
            :fitParent="true"
            :player-vars="playerVars"
            @ended="ended"
          ></youtube>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const getYotubePlaylistId = require("get-youtube-playlist-id");
//const getVideoId = require("get-video-id");
export default {
  name: "Home",
  data() {
    return {
      inputLink: null,
      playListLink: null,
      playerVars: {
        autoplay: 1,
        controls: 1,
        rel: 0,
        playsinline: 0,
      },
      videoList: [],
      randomMusic: true,
      loopMusic: false,
      videoId: null,
    };
  },
  methods: {
    playVideo() {
      console.log("playVideo");
      this.player.playVideo();
    },
    playing() {
      console.log("o/ we are watching!!!");
      window.addEventListener(
        "visibilitychange",
        (e) => e.stopImmediatePropagation(),
        true
      );
    },
    ready() {
      console.log("ready");
      this.playVideo();
    },
    ended() {
      console.log("end");
      if (this.loopMusic == false) {
        this.setVidoe();
      }
      this.playVideo();
    },
    getListVideo() {
      const vm = this,
        playListLink = vm.playListLink,
        listReg =
          "^(?:https?://)?(?:www.)?youtu.?be(?:.com)?.*?(?:v|list)=(.*?)(?:&|$)|^(?:https?://)?(?:www.)?youtu.?be(?:.com)?(?:(?!=).)*/(.*)$",
        videoReg = /^.*(youtu.be\/|v\/|u\/\w\/|embed\/|watch\?v=|\&v=)([^#\&\?]{11,11}).*/;

      if (playListLink) {
        if (playListLink.indexOf("list") != -1) {
          let listID = getYotubePlaylistId(playListLink),
            listVideoId = "";

          if (playListLink.match(videoReg)) {
            listVideoId =
              playListLink.match(videoReg).length > 2
                ? playListLink.match(videoReg)[2]
                : "";
          }

          //console.log(playListLink.indexOf("watch"));
          //console.log(playListLink.indexOf("list"));

          //var id = getYotubePlaylistId(vm.playListLink);
          //let listListID = vm.playListLink.match(listReg);
          //let videoListID = listListID[1];

          const youtubeLink = `${process.env.VUE_APP_YOUTUBEAPIPATH}?part=snippet%2CcontentDetails&maxResults=50&playlistId=${listID}&key=${process.env.VUE_APP_YOUTUBEKEY}`;

          vm.$http
            .get(youtubeLink, {
              withCredentials: false,
            })
            .then((res) => {
              console.log(res);
              vm.videoList = res.data.items;
              if (listVideoId != "") {
                vm.videoId = listVideoId;
              } else {
                vm.setVidoe();
              }
            })
            .catch((err) => {
              console.error(err);
            });
        }
      }
    },
    setLoop() {
      this.loopMusic = !this.loopMusic;
    },
    setRandom() {
      this.randomMusic = !this.randomMusic;
    },
    setVidoe() {
      if (this.videoId == null || this.randomMusic == true) {
        let videoCount = this.videoList.length,
          currentVideo = Math.floor(Math.random() * videoCount);
        this.videoId = this.videoList[currentVideo].snippet.resourceId.videoId;
      }
    },
  },
  computed: {
    player() {
      return this.$refs.youtube.player;
    },
  },
  components: {},
  mounted() {
    setTimeout(function () {
      window.addEventListener(
        "visibilitychange",
        (e) => e.stopImmediatePropagation(),
        true
      );
    }, 3000);
  },
};
</script>