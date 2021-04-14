<template lang=""> 
  <div class="tab-bar-item" @click="itemclick">
    <!-- <img src="../../assets/img/tabbar/001_首页.png" alt="">
    <div>首页</div> -->
    <div v-if="!isActive"><slot name="item-icon"></slot></div>
    <div v-else><slot name="item-icon-active"></slot></div>
    <div :style="activeStyle"><slot name="item-text"></slot></div>
  </div>
</template>
<script>
export default {
name:'TabBarItem',
data() {
  return {
    // isActive: true
  }
},
computed:{
  isActive(){
    return !this.$route.path.indexOf(this.path)
  },
  activeStyle(){
    return this.isActive ? {color: this.activeColor} : {}
  }
},
props:{
  path: String,
  activeColor:{
    type:String,
    default:'rgb(0, 132, 255)'
  }  
},
methods: {
  itemclick(){
    this.$router.replace('/location').catch(err => { console.log(err)})
    this.$router.replace(this.path)
  }
},
}
</script>
<style scoped>
  .tab-bar-item {
  flex: 1;
  text-align: center;
  height: 49px;
  font-size: 14px;
}
.tab-bar-item img{
  height: 24px;
  width: 24px;
  margin-top: 3px;
  vertical-align: middle;
  margin-bottom: 2px;
}
/* .active{
  color: rgb(0, 132, 255)
} */
</style>