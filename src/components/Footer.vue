<template>
  <div>
    <div class="footer">
      <span>
        Copyright © {{ year }}
      </span>
      <a href="https://github.com/cookieseventeen">cookieseventeen</a><span>. All
      rights reserved.</span>
    </div>
  </div>
</template>

<script>
export default {
  name: "Footer",
  data() {
    return {
      year: "",
    };
  },
  methods: {},
  created() {
    this.year = new Date().getFullYear();
  },
};
</script>
<style lang="scss" scoped>
.footer {
  background: none;
  color: #fff;
  position: fixed;
  bottom: 12px;
  width: 100%;
  left: 0;
  pointer-events: none;
  a{
    pointer-events: auto;
  } 
  span{
    opacity: 0.4;
  }
}
</style>