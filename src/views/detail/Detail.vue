<template lang="">
  <div>
    <h2>{{iid}}</h2>
  </div>
</template>
<script>
export default {
  name:'Detail',
  data() {
    return {
      iid:null
    }
  },
  created() {
    console.log('this.$route.params');
    this.iid = this.$route.params.iid
  },
}
</script>
<style scoped>
  
</style>