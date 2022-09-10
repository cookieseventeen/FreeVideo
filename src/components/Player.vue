<template>
  <div>
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
        <div class="player__list" >
          <div class="ctrl-bar">
            <div class="row">
              <div class="col">
                <a
                  class="ctrl-btn"
                  :class="{ 'ctrl-btn--disabled': !loopMusic }"
                  @click="setLoop"
                  title="重複播放"
                >
                  <i class="fas fa-undo"></i>重複播放
                </a>
              </div>
              <div class="col">
                <a
                  class="ctrl-btn"
                  :class="{ 'ctrl-btn--disabled': !randomMusic }"
                  @click="setRandom"
                  title="隨機播放"
                >
                  <i class="fas fa-random"></i>隨機播放
                </a>
              </div>
              <div class="col">
                <a class="ctrl-btn" @click="setVidoe" title="下一首">
                  <i class="fas fa-fast-forward"></i>下一首
                </a>
              </div>
            </div>
            <div class="row">
              <div class="col">
                <b-form-input class="quick-search"  v-model="quickSearch"></b-form-input>
              </div>
              <div class="col-md-auto order-by-col">
                <b-button @click="clearSearch()" class="clear-btn">
                  Clear
                </b-button>
              </div>
              <!--
              <div class="col-md-auto order-by-col">
                <b-button class="order-by" :class="{ 'order-by--ascending': !decreasing }" ><i class="fas fa-angle-double-down"></i></b-button>
              </div>
              -->
            </div>
          </div>
          <ul class="youtube-list" v-if="videoList">
            <li class="youtube-list__item"
              v-for="(item, key) in videoList"
              :key="key"
              :class="{'active': key === currentVideo,'youtube-list__item--hidden':pick(item.snippet.title)}"
              @click="setVidoe(key)"
             ref="playerItems">
              <div class="youtube-list__item-wrap">
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
                    {{ key }}- <div class="youtube-list__sort-text" v-html="searchText(item.snippet.title)"></div>
                  </div>
                </div>
              </div>
            </li>
          </ul>
        </div>
      </div>
      <div class="player__bar">
        <div class="col-md-8">
          <b-input-group prepend="Youtube 列表">
            <div
              class="player-input"
              :class="[historyStatus ? 'player-input--open-history' : '']"
            >
              <b-form-input v-model="playListLink"></b-form-input>
            </div>
            <b-input-group-append>
              <b-button variant="info" @click="getListVideo('')"
                >載入播放列表</b-button
              >
            </b-input-group-append>
          </b-input-group>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const getYotubePlaylistId = require("get-youtube-playlist-id");
export default {
  name: "Player",
  data() {
    return {
      playListLink: null,
      decreasing: true,
      quickSearch:null,
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
      randomMusic: false,
      loopMusic: false,
      videoId: null,
      nextPageToken: "",
      prevPageToken: "",
      historyStatus: false,
    };
  },
  methods: {
    playVideo() {
     
      
      this.player.playVideo();
      
    },
    playing() {
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
        videoReg =
          /^.*(youtu.be\/|v\/|u\/\w\/|embed\/|watch\?v=|\&v=)([^#\&\?]{11,11}).*/;

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
        this.videoList = [];
        this.getVideo(listID);
      }
    },
    getVideo(listID, pageTokenData) {
      const vm = this;
      let httpParams = {
        part: "snippet",
        playlistId: listID,
        maxResults: 50,
        key: process.env.VUE_APP_YOUTUBEKEY,
        pageToken: pageTokenData,
      };

      vm.$http
        .get(process.env.VUE_APP_YOUTUBEAPIPATH, {
          withCredentials: false,
          params: httpParams,
        })
        .then((res) => {
          console.log(res);
          vm.videoList = vm.videoList.concat(res.data.items.filter(item=>item?.snippet?.title!=='Private video'));
          vm.videoListCounter = res.data.pageInfo.totalResults;
          vm.nextPageToken = res.data.nextPageToken;
          vm.prevPageToken = res.data.prevPageToken;

          if (!res.data.nextPageToken || vm.videoList?.length > 100) {
            if (!vm.videoId) {
              vm.setVidoe();
            } else {
              vm.currentVideo = vm.videoList.findIndex(
                (element) => vm.videoId == element.snippet.resourceId.videoId
              );
            }
            //成功抓到數據的時候，將連結存到youtubeLink
            console.log(res.data);
            //vm.addHistory();
          } else {

            this.getVideo(listID, res.data.nextPageToken);
          }
        })
        .catch((err) => {
          console.error(err);
        });
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
          if(vm.currentVideo!== null){
            vm.currentVideo++;
          }else{
            vm.currentVideo = 0;
          }
          
        } else {
          vm.currentVideo = 0;
        }
      }
      if (typeof orderNum === "number") {
        vm.currentVideo = orderNum;
      }
      vm.videoId = this.videoList[vm.currentVideo].snippet.resourceId.videoId;

      //置中
      const ListScrollTop=this.$refs.playerItems[vm.currentVideo].scrollIntoView({behavior: "smooth", block: "center"});;

    },
    addHistory(){
      localStorage.setItem('freeVidoeHistory', this.playListLink);
    }
    ,
    pick(title){
      if(this?.quickSearch){
        if(title.indexOf(this?.quickSearch)!== -1){
          return false
        }else{
          return true
        }
      }else{
        return false
      }
    },
    clearSearch(){
      this.quickSearch='';
    },
    searchText(text){
      if(this?.quickSearch){
        let keywordReg=new RegExp(this.quickSearch,"g");
        return text.replace(keywordReg,`<span>${this.quickSearch}</span>`)
      }else{
        return text
      }
    }
  },
  computed: {
    player() {
      return this.$refs.youtube.player;
    },
    fullHeight() {
      return window.innerHeight;
    },
  },
  mounted() {
    
    const vm = this;
    let playerHistory = localStorage.getItem("historyLink");
    if (playerHistory) {
      playerHistory = JSON.parse(playerHistory);
      this.playListLink=playerHistory;
    }
    
  }
};
</script>

<style lang="scss" scoped>
@import "../assets/scss/all.scss";
$dark-color: #030303;

.ctrl-btn {
  display: inline-flex;
  padding: 12px 0;
  width: 100%;
  background: #030303;
  color: #fff;
  border: 1px solid transparent;
  transition: border-color 0.4s ease;
  align-items: center;
  justify-content: center;
  &:hover {
    border-color: #fff;
  }
  .fas {
    margin-right: 8px;
  }
  &--disabled {
    color: #444;
  }
}

.player {
  position: relative;
  background: #030303;
  &__bar {
    position: absolute;
    background: lighten($color: $dark-color, $amount: 15);
    height: 72px;
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
    top: 76px;
    bottom: 0;
  }
  &__video {
    position: absolute;
    left: 0;
    width: calc(100% - 460px);
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
    width: 436px;
    height: 100%;
    padding-top: 42px;
    overflow: hidden;
    //border-left: 1px solid #fff;
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
  padding-left: 0;
  top: 88px;
  bottom: 0;
  right: 0;
  width: 100%;
  &__sort-text{
    display: inline;
    ::v-deep{
      span{
        background: #ffff00;
        color: #000;
      }
    }
  }
  &__item{
    &--hidden{
      display: none;
    }
  }
  &__item-wrap {
    display: flex;
    padding: 8px;
    transition: box-shadow 0.4s ease;
  }
  li {
    padding: 8px;
    cursor: pointer;
    & + li {
      margin-top: 8px;
    }
    &.active {
      .youtube-list__item-wrap {
        background: lighten($color: $dark-color, $amount: 15);
        //box-shadow: 0 0 12px 4px rgba($color: #fff, $alpha: 1);
        box-shadow: 0 0 6px 3px white;
      }
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
.player-input {
  position: relative;
  flex: 1 1 auto;
  width: 1%;
  min-width: 0;
  margin-bottom: 0;
  &--open-history &__history {
    opacity: 1;
    pointer-events: auto;
  }
  &__history {
    position: absolute;
    top: 100%;
    left: 0;
    width: 100%;
    background: #e5e5e5;
    transition: opacity 0.6s ease;
    opacity: 0;
    pointer-events: none;

    ul,
    li {
      list-style: none;
      padding: 0;
    }
    > ul {
      padding: 15px;
      border-radius: 5px;
    }
    li {
      text-align: left;
    }
    li + li {
      margin-top: 8px;
    }
  }
  &__history-link {
    display: block;
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
  }
  &__history-item {
    margin-top: 4px;
  }
  &__history-item-list {
    display: flex;
    overflow-x: auto;
  }
  &__history-item-list-items {
    flex: 0 0 auto;
  }
  &__history-wrap {
    cursor: pointer;
    transition: background 0.4s ease;
    &:hover {
      background: darken($color: #e5e5e5, $amount: 10);
    }
  }
}
.preview-card {
  display: flex;
  align-items: center;
  &__pic {
    width: 60px;
    img {
      max-width: 100%;
    }
  }
  &__text {
    padding-left: 8px;
  }
}
.history-preview-list {
  display: flex;
  overflow-x: auto;
  margin: 0 -6px;
  &__item {
    display: flex;
    padding: 0 3px;
    flex: 0 0 auto;
  }
  &__text {
    white-space: nowrap;
  }
}
.quick-search{
  width:100%;
}
.order-by-col{
  padding-left: 0;
}
.order-by{
  margin-right: 15px;
}
</style>

