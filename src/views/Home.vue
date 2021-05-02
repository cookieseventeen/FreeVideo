<template>
  <div class="player" :style="{ height: fullHeight + 'px' }">
    <div class="player__content">
      <div class="player__video">
        <youtube
          class="youtube-video"
          :video-id="videoId"
          ref="youtube"
          @playing="playing"
          :resize="true"
          :fitParent="true"
          :player-vars="playerVars"
          @ended="ended"
        ></youtube>
      </div>
      <div class="player__list">
        <div class="ctrl-bar">
          <div class="row">
            <div class="col-6">
              <button
                type="button"
                class="btn ctrl-bar__btn"
                v-if="prevPageToken"
                @click="getListVideo(prevPageToken)"
                title="上一頁"
              >
                <i class="fas fa-chevron-left"></i>上一頁
              </button>
            </div>
            <div class="col-6">
              <button
                type="button"
                class="btn ctrl-bar__btn"
                v-if="nextPageToken"
                @click="getListVideo(nextPageToken)"
                title="下一頁"
              >
                下一頁<i class="fas fa-chevron-right"></i>
              </button>
            </div>
          </div>
        </div>
        <ul class="youtube-list" v-if="videoList">
          <li
            v-for="(item, key) in videoList"
            :key="key"
            :class="[key === currentVideo ? 'active' : '']"
            @click="setVidoe(key)"
          >
            <template v-if="item.snippet.thumbnails.default">
              <div
                class="youtube-list__img"
                :style="{
                  backgroundImage:
                    'url(' + item.snippet.thumbnails.default.url + ')',
                }"
              >
                <img
                  :src="item.snippet.thumbnails.default.url"
                  :alt="item.snippet.title"
                />
              </div>
            </template>
            <div class="youtube-list__text-info">
              <div class="youtube-list__title">
                {{ key }}-{{ item.snippet.title }}
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>

    <!--
    <div class="container-fluid">
      <div class="row">
        <div class="col-md-8" v-if="videoId">
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
        <div class="col-md-4"></div>
      </div>
    </div>
    -->
    <div class="player__bar">
      <div class="col-md-8">
        <b-input-group prepend="Youtube 列表">
          <b-form-input v-model="playListLink"></b-form-input>
          <b-input-group-append>
            <b-button variant="info" @click="getListVideo"
              >載入播放列表</b-button
            >
          </b-input-group-append>
        </b-input-group>
      </div>
      <div class="col">
        <button
          type="button"
          class="btn btn-warning"
          :class="{ disabled: !loopMusic }"
          @click="setLoop"
          title="重複播放"
        >
          <i class="fas fa-undo"></i>重複播放
        </button>
      </div>
      <div class="col">
        <button
          type="button"
          class="btn btn-info"
          :class="{ disabled: !randomMusic }"
          @click="setRandom"
          title="隨機播放"
        >
          <i class="fas fa-random"></i>隨機播放
        </button>
      </div>
      <div class="col">
        <button
          type="button"
          class="btn btn-info"
          @click="setVidoe"
          title="下一首"
        >
          <i class="fas fa-fast-forward"></i>下一首
        </button>
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
      videoListID: null,
      videoListVideoID: null,
      videoList: [],
      videoListCounter: 0,
      currentVideo: null,
      randomMusic: true,
      loopMusic: false,
      videoId: null,
      nextPageToken: "",
      prevPageToken: "",
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
    getListVideo(pageTokenData) {
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

        let httpParams = {
          part: "snippet,contentDetails",
          playlistId: listID,
          maxResults: 50,
          key: process.env.VUE_APP_YOUTUBEKEY,
          pageToken: pageTokenData,
        };
        console.log("httpParams", httpParams);
        vm.$http
          .get(process.env.VUE_APP_YOUTUBEAPIPATH, {
            withCredentials: false,
            params: httpParams,
          })
          .then((res) => {
            console.log(res.data);

            vm.videoList = res.data.items;
            vm.videoListCounter = vm.videoList.length;
            vm.nextPageToken = res.data.nextPageToken;
            vm.prevPageToken = res.data.prevPageToken;

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
    setVidoe(orderNum = null) {
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
      if (typeof orderNum === "number") {
        console.log(orderNum);
        vm.currentVideo = orderNum;
      }
      vm.videoId = this.videoList[vm.currentVideo].snippet.resourceId.videoId;
    },
  },
  computed: {
    player() {
      return this.$refs.youtube.player;
    },
    fullHeight() {
      return window.innerHeight;
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
function getVideoData() {}
</script>

<style lang="scss" scoped>
$dark-color: #030303;

.player {
  position: relative;
  background: #030303;
  &__bar {
    position: absolute;
    background: lighten($color: $dark-color, $amount: 15);
    height: 100px;
    width: 100%;
    left: 0;
    top: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    .btn {
      margin: 0 8px;
      i {
        margin-right: 4px;
      }
    }
  }
  &__content {
    position: absolute;
    left: 0;
    right: 0;
    top: 120px;
    bottom: 0;
  }
  &__video {
    position: absolute;
    left: 0;
    width: 64%;
    height: 100%;
    ::v-deep iframe {
      position: absolute;
      top: 0;
      left: 0;
      width: 100% !important;
      height: 100% !important;
    }
  }
  &__list {
    position: absolute;
    right: 0;
    width: 30%;
    height: 100%;
    padding-top: 42px;
    overflow: hidden;
  }
}
.ctrl-bar {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  &__btn {
    color: #fff;
    &:hover {
      color: #fff;
      text-shadow: 1px 1px 1px rgba(255, 255, 255, 1);
    }
  }
}
.youtube-list {
  position: absolute;
  overflow-y: auto;
  overflow-x: hidden;
  top: 42px;
  bottom: 0;
  right: 0;
  width: 100%;
  li {
    display: flex;
    padding: 8px;
    cursor: pointer;
    & + li {
      margin-top: 8px;
    }
    &.active {
      background: lighten($color: $dark-color, $amount: 15);
    }
  }
  &__img {
    flex: 1;
    background-size: cover;
    background-position: center center;

    &::before {
      content: "";
      display: block;
      padding-top: 56.25%;
    }
    img {
      display: none;
    }
  }
  &__text-info {
    display: flex;
    width: 80%;
    padding: 0 15px;
    justify-content: left;
    align-items: center;
  }
  &__title {
    color: #fff;
    text-align: left;
  }
}
</style>

