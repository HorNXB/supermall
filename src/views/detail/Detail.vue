<template lang="">
  <div id="detail">
    <detail-nav-bar class="detail-nav" @titleClick="titleClick" ref="nav"></detail-nav-bar>
    <scroll class="content" ref="scroll" :probe-type="3" @scroll="contentScroll">
      <detail-swiper :top-images="topImages"></detail-swiper>
      <detail-base-info :goods="goods" id="detail-base-info"></detail-base-info>
      <detail-shop-info :shop="shop"></detail-shop-info>
      <detail-goods-info :detail-info="detailInfo" @imageLoad="imageLoad" id="detail-goods-info"></detail-goods-info>
      <detail-param-info :param-info="paramInfo" id="detail-param-info" ref="params"></detail-param-info>
      <detail-comment-info :comment-info="commentInfo" id="detail-comment-info" ref="comment"></detail-comment-info>
      <goods-list :goods="recommends" id="goods-list" ref="recommend"></goods-list>
    </scroll>
    <back-top @click.native="backClick" v-show="isShowBackTop"></back-top>
  </div>
</template>
<script>
  import DetailNavBar from './childComps/DetailNavBar'
  import DetailSwiper from './childComps/DetailSwiper'
  import DetailBaseInfo from './childComps/DetailBaseInfo'
  import DetailShopInfo from './childComps/DetailShopInfo'
  import DetailGoodsInfo from './childComps/DetailGoodsInfo'
  import DetailParamInfo from './childComps/DetailParamInfo'
  import DetailCommentInfo from './childComps/DetailCommentInfo'

  import Scroll from 'components/common/scroll/Scroll'
  import GoodsList from 'components/content/goods/GoodsList'
  import BackTop from 'components/content/backTop/BackTop'

  import {getDetail, Goods, Shop, GoodsParam, getRecommend} from 'network/detail'
  import {debounce} from 'common/utils'
  import {itemListenerMixin} from 'common/mixin'
export default {
  name:'Detail',
  data() {
    return {
      iid:null,
      topImages: [],
      goods: {},
      shop: {},
      detailInfo: {},
      paramInfo: {},
      commentInfo: {},
      recommends: [],
      scrollY: [],
      getScrollY: null,
      scrollId: null,
      currentIndex: 0,
      isShowBackTop: false

    }
  },
  mixins: [itemListenerMixin],
  components: {
    DetailNavBar,
    DetailSwiper,
    DetailBaseInfo,
    DetailShopInfo,
    DetailGoodsInfo,
    DetailParamInfo,
    DetailCommentInfo,
    Scroll,
    GoodsList,
    BackTop
  },
  methods: {
    imageLoad() {
      this.$refs.scroll.refresh()
      this.getScrollY()
    },
    titleClick(index) {
      // console.log(index);
      // 第一种方案,通过scrollToElement()实现对组件的位置滚动
      switch (index) {
        case 0:
          this.scrollId = "#detail-base-info"
          break;
      
        case 1:
          this.scrollId = "#detail-param-info"
          break;

        case 2:
          this.scrollId = "#detail-comment-info"
          break;
        
        case 3:
          this.scrollId = "#goods-list"
      }
      this.$refs.scroll.scrollToElement(this.scrollId, 500)
      // this.$refs.scroll.scrollTo(0, -this.scrollY[index], 500)
    },
    contentScroll(position) {
      const positionY = -position.y
      let length = this.scrollY.length
      for(let i=0; i < length; i++) {
        if (this.currentIndex !== i && ((i < length - 1 && positionY >= this.scrollY[i] && positionY <
        this.scrollY[i + 1]) || (i == length - 1 && positionY >= this.scrollY[i]))) {
          this.currentIndex = i;
          // console.log(this.currenstIndex);
          this.$refs.nav.currentIndex = this.currentIndex
        }
      }
    },
    backClick() {
      this.$refs.scroll.scrollTo(0, 0)
    },
    contentScroll(position) {
      // console.log(position);
      // 1.判断BackTop是否显示
      this.isShowBackTop = Math.abs(position.y) > 1500
      // 2.决定tabControl是否吸顶
      this.isTabFlexd = Math.abs(position.y) > this.tabOffsetTop
    }
  },
  created() {
    // 1.保存传入的iid
    this.iid = this.$route.params.iid

    // 2.根据iid请求详情数据
    getDetail(this.iid).then(res => {
      // 2-1.获取顶部的图片轮播数据
      const data = res.result;
      this.topImages = data.itemInfo.topImages

      // 2-2.获取商品信息
      this.goods = new Goods(data.itemInfo, data.columns, data.shopInfo.services)

      // 2-3.创建商家信息
      this.shop = new Shop(data.shopInfo)

      // 2-4.保存商品的详情数据
      this.detailInfo = data.detailInfo;
      // console.log(this.detailInfo);

      // 2-5.获取参数的信息
      this.paramInfo = new GoodsParam(data.itemParams.info, data.itemParams.rule)

      // 2-6.取出评论的信息
      if (data.rate.cRate !== 0) {
        this.commentInfo = data.rate.list[0]
      }
    })

    // 3.请求推荐数据
    getRecommend().then(res => {
      this.recommends = res.data.list
    })

    // 4.给scrollY赋值进行防抖处理
    this.getScrollY = debounce(() => {
      this.scrollY = []
      this.scrollY.push(0)
      this.scrollY.push(this.$refs.params.$el.offsetTop)
      this.scrollY.push(this.$refs.comment.$el.offsetTop)
      this.scrollY.push(this.$refs.recommend.$el.offsetTop)
      console.log(this.scrollY);
    })
  },
  mounted() {
    
  },
  destroyed() {
    this.$bus.$off('itemImageLoad' ,this.itemImgListener)
  }
}
</script>
<style scoped>
  #detail {
    position: relative;
    z-index: 9;
    background-color: #fff;
    height: 100vh;
  }

  .detail-nav {
    position: relative;
    z-index: 9;
    background-color: #fff;
  }

  .content {
    height: calc(100% - 44px);
  }
</style>