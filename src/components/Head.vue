<template>
  <div>
    <div class="menu" :class="{ 'menu--open': menuStatus }">
      <div class="menu__btn">
        <button
          v-if="false"
          class="hamburger hamburger--squeeze"
          type="button"
          :class="{ 'is-active': menuStatus }"
          @click="toggleMenuStatus()"
        >
          <span class="hamburger-box">
            <span class="hamburger-inner"></span>
          </span>
        </button>
      </div>
      <div class="menu__content">
        <ul class="menu__list">
          <!--
          <li>
            <router-link class="py-2 d-none d-md-inline-block" to="/">
              -搜尋
            </router-link>
          </li>
          -->
          <li>
            <router-link class="py-2 d-none d-md-inline-block" to="/">
              -播放器
            </router-link>
          </li>
          <li>
            <router-link class="py-2 d-none d-md-inline-block" to="/history">
              -歷史紀錄
            </router-link>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Head",
  data() {
    return {
      menuStatus: false,
    };
  },
  methods: {
    toggleMenuStatus() {
      this.menuStatus = !this.menuStatus;
      console.log(this.menuStatus);
    },
  },
  created() {},
  beforeRouteLeave(to, from, next) {
    console.log(to);
    console.log(from);
    console.log(next);
  },
};
</script>
<style lang="scss" scoped>
@import "~hamburgers/_sass/hamburgers/hamburgers";
.menu {
  position: absolute;
  right: 15px;
  top: 10px;
  z-index: 99;
  &__btn {
    position: relative;
    z-index: 20;
  }
  &__content {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    overflow: hidden;
    pointer-events: none;
    transition: opacity 0.4s ease;
    &::before,
    &::after {
      content: "";
      display: block;
      position: absolute;
      width: 50%;
      height: 100%;
      background: #fff;
      transition: transform 0.4s ease;
    }

    &::before {
      left: 0;
      top: 0;
      transform: translateY(100%);
    }
    &::after {
      right: 0;
      top: 0;
      transform: translateY(-100%);
    }
  }

  &--open &__content {
    opacity: 1;
    pointer-events: auto;
    &::before,
    &::after {
      transform: translateY(0);
    }
  }

  &__list {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    list-style: none;
    padding: 0;
    z-index: 2;
    opacity: 0;
    transition: opacity 0.4s ease;
    li {
      font-size: 3em;
      text-align: left;
      overflow: hidden;
      a {
        color: #000;
        cursor: pointer;
      }
    }
  }
  &--open &__list {
    transition-delay: 0.4s;
    opacity: 1;
  }
  .hamburger {
    outline: none !important;
  }
  .hamburger-inner {
    background: #fff;
    &::before,
    &::after {
      background: #fff;
    }
  }
}
</style>