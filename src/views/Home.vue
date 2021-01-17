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
      videoListCounter: 0,
      currentVideo: null,
      randomMusic: true,
      loopMusic: false,
      videoId: null,
    };
  },
  methods: {
    playVideo() {
      //console.log("playVideo");
      this.player.playVideo();
    },
    playing() {
      //console.log("o/ we are watching!!!");
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

      //clear video
      vm.videoId = "";

      //判斷有沒有video ID
      if (playListLink.match(videoReg)) {
        vm.videoId =
          playListLink.match(videoReg).length > 2
            ? playListLink.match(videoReg)[2]
            : "";
      }
      //判斷是不是列表，如果是取出列表ID向youtube查詢．
      if (playListLink.indexOf("list") != -1) {
        let listID = getYotubePlaylistId(playListLink);
        const youtubeLink = `${process.env.VUE_APP_YOUTUBEAPIPATH}?part=snippet%2CcontentDetails&maxResults=50&playlistId=${listID}&key=${process.env.VUE_APP_YOUTUBEKEY}`;

        vm.$http
          .get(youtubeLink, {
            withCredentials: false,
          })
          .then((res) => {
            //console.log(res);
            vm.videoList = res.data.items;
            vm.videoListCounter = vm.videoList.length;
            if (!vm.videoId) {
              vm.setVidoe();
            } else {
              vm.currentVideo = vm.videoList.findIndex(
                (element) => vm.videoId == element.snippet.resourceId.videoId
              );
            }
            //成功抓到數據的時候，將連結存到youtubeLink
            localStorage.setItem("youtubeLink", vm.playListLink);
          })
          .catch((err) => {
            console.error(err);
          });
      }
    },
    setLoop() {
      this.loopMusic = !this.loopMusic;
    },
    setRandom() {
      this.randomMusic = !this.randomMusic;
    },
    setVidoe() {
      let vm = this;
      //如果是亂數產生
      if (vm.videoId == null || vm.randomMusic == true) {
        let videoCount = vm.videoList.length;
        vm.currentVideo = Math.floor(Math.random() * vm.videoListCounter);
      }

      //非亂數產生
      if (vm.videoId == null || vm.randomMusic == false) {
        if (vm.currentVideo < vm.videoListCounter - 1) {
          vm.currentVideo++;
        } else {
          vm.currentVideo = 0;
        }
      }
      vm.videoId = this.videoList[vm.currentVideo].snippet.resourceId.videoId;
    },
  },
  computed: {
    player() {
      return this.$refs.youtube.player;
    },
  },
  components: {},
  mounted() {
    const vm = this;
    let youtubeLink = localStorage.getItem("youtubeLink");
    if (youtubeLink) {
      vm.playListLink = youtubeLink;

      setTimeout(() => {
        vm.getListVideo();
        vm.playVideo();
      }, 1000);
    }
    /*
    setTimeout(function () {
      window.addEventListener(
        "visibilitychange",
        (e) => e.stopImmediatePropagation(),
        true
      );
    }, 3000);
    */
  },
};
</script>