<template>
    <transition name="fade">
      <v-touch class="lg-preview-wrapper" v-show="preview.show" v-on:swipeleft="nextAction" v-on:swiperight="preAction" v-on:tap="leave">
          <div class="lg-preview-loading" v-show="preview.loading"><div></div></div>
          <img
            class="lg-preview-img"
            v-if="preview.current.src"
            :src="preview.current.src"
            :alt="preview.current.title"
            v-show="!preview.loading"
          >
          <div class="lg-preview-title" v-if="preview.isTitleEnable&&preview.current.title" v-show="!preview.loading">
            {{preview.current.title}}
          </div>
          <div class="lg-preview-nav-left" v-if="preview.isHorizontalNavEnable">
              <span class="lg-preview-nav-arrow" @click="preAction" ></span>
          </div>
          <div class="lg-preview-nav-right" v-if="preview.isHorizontalNavEnable">
              <span class="lg-preview-nav-arrow" @click="nextAction"></span>
          </div>
      </v-touch>
    </transition>
</template>

<script>
  import Vue from 'vue'
  var VueTouch = require('vue-touch')
  Vue.use(VueTouch, {name: 'v-touch'})

  export default {
    name: 'Preview',
    computed: {
      preview () {
        return window.LOGIC_EVENT_BUS.LOGIC_PREVIEW
      }
    },
    watch: {
    },
    methods: {
      leave (e) {
        if ((this.preview.show) && (e.target.className.indexOf('lg-preview-nav-arrow') !== 0)) {
          this.close()
        }
      },
      close () {
        this.preview.show = false
      },
      preAction () {
        this.preview.loading = true
        let index = this.preview.list.indexOf(this.preview.current)
        if (index === 0) {
          this.preview.loading = false
          return
        }
        index--
        this.preview.current = this.preview.list[index]
        const img = new window.Image()
        img.src = this.preview.current.src
        img.onload = function () {
          setTimeout(() => {
            window.LOGIC_EVENT_BUS.LOGIC_PREVIEW.loading = false
          }, 500)
        }
      },
      nextAction () {
        this.preview.loading = true
        let index = this.preview.list.indexOf(this.preview.current)
        if (index === this.preview.list.length - 1) {
          this.preview.loading = false
          return
        }
        index++
        this.preview.current = this.preview.list[index]
        const img = new window.Image()
        img.src = this.preview.current.src
        img.onload = function () {
          setTimeout(() => {
            window.LOGIC_EVENT_BUS.LOGIC_PREVIEW.loading = false
          }, 500)
        }
      }
    }
  }
</script>

<style scoped>
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s
}
.fade-enter, .fade-leave-active {
  opacity: 0
}

.touch-box {
  width: auto;
  height: auto;
}

.lg-preview-wrapper {
    position: fixed;
    top:0;
    left:0;
    width: 100%;
    height: 100%;
    text-align: center;
    box-sizing: border-box;
    background: rgba(0,0,0,0.9);
    z-index: 10000;
}

.lg-preview-loading {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 50px;
  height: 50px;
  margin-top: -25px;
  margin-left: -25px;
}

.lg-preview-loading > div {
  border-radius: 100%;
  margin: 2px;
  -webkit-animation-fill-mode: both;
          animation-fill-mode: both;
  border: 2px solid #fff;
  border-bottom-color: transparent;
  height: 25px;
  width: 25px;
  background: transparent;
  display: inline-block;
  -webkit-animation: rotate 0.75s 0s linear infinite;
          animation: rotate 0.75s 0s linear infinite;
}

@keyframes rotate {
  0% {
    -webkit-transform: rotate(0deg) scale(1);
            transform: rotate(0deg) scale(1); }

  50% {
    -webkit-transform: rotate(180deg) scale(0.6);
            transform: rotate(180deg) scale(0.6); }

  100% {
    -webkit-transform: rotate(360deg) scale(1);
            transform: rotate(360deg) scale(1); }
}

.lg-preview-img {
    max-width: 100%;
    max-height: 100%;
    display: block;
    position: absolute;
    left: 0;
    top: 0;
    bottom: 0;
    right: 0;
    margin: auto;
}

.lg-preview-nav-arrow {
    position: absolute;
    right: 0;
    top: 50%;
    width: 50px;
    height: 50px;
    margin-top: -40px;
    display: block;
    border-top: 1px solid #fff;
    border-right: 1px solid #fff;
}

.lg-preview-nav-left,
.lg-preview-nav-right {
    position: absolute;
    height: 100%;
    width: 50%;
    top: 0;
    color: #fff;
    transition: opacity .2s;
}

.lg-preview-nav-left .lg-preview-nav-arrow,
.lg-preview-nav-right .lg-preview-nav-arrow{
    transition: background .3s;
}

.lg-preview-nav-left {
    left: 0;

}
.lg-preview-nav-left .lg-preview-nav-arrow {
    left: 0;
    margin-left: 100px;
    transform:rotate(-135deg);
}

.lg-preview-nav-right {
    right: 0;
}

.lg-preview-nav-right .lg-preview-nav-arrow {
    right: 0;
    margin-right: 100px;
    transform:rotate(45deg);
}

.lg-preview-title {
    position: absolute;
    bottom: 0;
    text-align: center;
    width: 100%;
    color: #fff;
    background: rgba(0,0,0,.5);
    box-sizing: border-box;
    font-size: 0.8rem;
    height: 2rem;
    line-height: 2rem;
}

</style>
