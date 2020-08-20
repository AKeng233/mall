<template>
  <div>
    <nav-bar class="nav-bar"><div class="nav-bar-item" slot="center">购物街</div></nav-bar>
    <home-swiper :banners="banners"></home-swiper>
    <home-recommend :recommends="recommends"></home-recommend>
  </div>
</template>

<script>
import NavBar from 'components/common/navbar/NavBar'
import HomeSwiper from './childComps/HomeSwiper'
import HomeRecommend from './childComps/HomeRecommend'
import {getHomeMultidata} from 'network/home'
export default {
  name: 'Home',
  data() {
    return {
      banners: [],
      recommends: []
    }
  },
  components: {
    NavBar,
    HomeSwiper,
    HomeRecommend
  },
  created() {
    // 请求多个数据
    getHomeMultidata().then(res=>{
      console.log(res)
      this.banners = res.data.banner.list;
      this.recommends = res.data.recommend.list
    })
  }
}
</script>

<style>
  .nav-bar{
    background-color: #FF8198;
    color: #fff;
    font-weight: 700;
  }
</style>