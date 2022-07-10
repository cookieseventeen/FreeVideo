<template>
  <div>
    <Head></Head>
    <Player></Player>
    <!--
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
      <div class="player__bar">
        <div class="col-md-8">
          <b-input-group prepend="Youtube 列表">
            <div
              class="player-input"
              :class="[historyStatus ? 'player-input--open-history' : '']"
            >
              <b-form-input v-model="playListLink"></b-form-input>
              <div class="player-input__history">
                <ul>
                  <li v-for="(item, key) in getHistory" :key="key">
                    <div
                      class="player-input__history-wrap"
                      @dblclick="setHistory(item)"
                    >
                      <span class="player-input__history-link">{{
                        item.historyLink
                      }}</span>
                      <div class="player-input__history-item">
                        <div class="history-preview-list">
                          <div
                            class="history-preview-list__item"
                            v-for="(previewItem, key) in item.data.items"
                            :key="key"
                          >
                            <div class="preview-card">
                              <div class="preview-card__pic">
                                <img
                                  :src="
                                    previewItem.snippet.thumbnails.default.url
                                  "
                                  :alt="previewItem.snippet.title"
                                />
                              </div>
                              <div class="preview-card__text">
                                {{ previewItem.snippet.title }}
                              </div>
                            </div>
                          </div>
                        </div>
                      </div>
                    </div>
                  </li>
                </ul>
              </div>
            </div>
            <b-input-group-append>
              <b-button variant="info" @click="getListVideo('')"
                >載入播放列表</b-button
              >

              <b-button variant="info" @click="toggleHistory()"
                ><i class="fas fa-history"></i
              ></b-button>
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
    -->
    <div class="main">
      <router-view></router-view>
    </div>
    <Footer></Footer>
  </div>
</template>
<script>
import Head from "../components/Head";
import Footer from "../components/Footer";
import Player from "../components/Player";
export default {
  name: "Home",
  components: {
    Head,
    Footer,
    Player,
  },
  data() {
    return {};
  },
  methods: {},
  computed: {},
  mounted() {},
};
</script>

<style lang="scss" scoped>
@import "../assets/scss/all";
</style>

