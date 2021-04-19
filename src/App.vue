<template>
  <div id="app">
    <keep-alive>
      <router-view/>
    </keep-alive>
    <main-tab-bar/>
  </div>
</template>

<script>
  import MainTabBar from 'components/content/mainTabbar/MainTabBar'

  export default {
    name: 'app',
    components: {
      MainTabBar
    },
    mounted () {
      document.body.addEventListener('touchmove', function (e) {
          e.preventDefault() // 阻止默认的处理方式(阻止下拉滑动的效果)
      }, {passive: false}) // passive 参数不能省略，用来兼容ios和android
    }
  }
  
</script>

<style>
  @import "assets/css/base.css";
</style>
