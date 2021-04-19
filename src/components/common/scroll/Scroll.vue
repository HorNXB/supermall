<template lang="">
  <div class="warpper" ref="warpper">
    <div class="content">
      <slot ></slot>
    </div>
  </div>
</template>
<script>
  import BScroll from 'better-scroll'
export default {
  name: 'Scroll',
  data() {
    return {
      scroll: null,
    }
  },
  props: {
    probeType: {
      type: Number,
      default: 0
    },
    pullUpLoad: {
      type: Boolean,
      default: false
    }
  },
  methods: {
    scrollTo(x, y, time=500){
      this.scroll && this.scroll.scrollTo(x, y, time)
    },
    finishPullUp(){
      this.scroll.finishPullUp();
    },
    refresh(){
      // console.log('-----');
      this.scroll && this.scroll.refresh();
    },
    saveScrollY(){
      return this.scroll ? this.scroll.y : 0
    }
  },
  mounted() {
    // 1.创建BScroll对象
    this.scroll = new BScroll(this.$refs.warpper,{
      click: true,
      probeType: this.probeType,
      pullUpLoad:this.pullUpLoad
    }),
    // 2.监听滚动的位置
    this.scroll.on('scroll',(position) => {
      // console.log(position);
      this.$emit('scroll',position)
    }),
    // 3.监听下拉事件
    this.scroll.on('pullingUp',() => {
      this.$emit('pullingUp')
    })
  },
}
</script>
<style scoped>
  
</style>