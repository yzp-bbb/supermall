<template>
  <div id="home">
  <nav-bar class="home-nav"><div slot="center">购物街</div></nav-bar>
  <scroll  class="content" 
  ref="scroll" 
  :probe-type="3" 
  :pull-up-load="true"
  @scroll="contnetscroll"
  @pullingUp="loadMore"
  >
     <home-swiper :banners="banners"/>
     <home-recommend-view :recommends="recommends"/>
     <home-feature-view/>
     <tab-control :titles="['流行','新款','精选']"
                  class="tab-control"
                  @tabclick="tabclick"
     />
     <good-list :goods="showgoods" ></good-list>

  </scroll>
  <!-- native监听组件点击 -->
    <back-top @click.native="backTop" v-show="isshowBackTop"/>
  </div>

</template>

<script>
  import HomeSwiper from "./childComps/HomeSwiper"
  import HomeRecommendView from "./childComps/HomeRecommendView"
  import HomeFeatureView from "./childComps/HomeFeatureView"

  import tabControl from "components/content/tabControl/TabControl"
  import NavBar from 'components/common/navbar/NavBar'
  import GoodList from 'components/content/goods/GoodList'
  import Scroll from 'components/common/scroll/Scroll'
  import BackTop from 'components/content/backTop/BackTop'

  import {getHomeMulitidata,getHomeGoods} from "network/home"




  export default {
    name: "Home",
    components:{
      NavBar,
      HomeSwiper,
      HomeRecommendView,
      HomeFeatureView,
      tabControl,
      GoodList,
      Scroll,
      BackTop
    },
    data(){
      return{
        banners:[],
        recommends:[],
        goods:{
          'pop':{page:0,list:[]},
          'new':{page:0,list:[]},
          'sell':{page:0,list:[]},
        },
        currenttype:'pop',
        isshowBackTop:'false'
      }
    },
    computed:{
      showgoods(){
        return this.goods[this.currenttype].list
      }
    },
    created() {
      this.getHomeMulitidata()

      this.getHomeGoods('pop')
      this.getHomeGoods('new')
      this.getHomeGoods('sell')
    },
    mounted(){

    },
    methods:{
      //事件监听相关
      tabclick(index){
        switch (index) {
        case 0:
          this.currenttype='pop'
          break
        case 1:
          this.currenttype='new'
          break
        case 2:
          this.currenttype='sell'
          break
      }
      },
      backTop(){
        this.$refs.scroll.scroll.scrollTo(0,0,500)
      },
      contnetscroll(postion){
       this.isshowBackTop=-postion.y > 1000
       
      },
      loadMore(){
        // console.log("上拉加载更多");
        this.getHomeGoods(this.currenttype)
      },


      //网络请求相关
      getHomeMulitidata(){
        getHomeMulitidata().then(res => {
          // console.log(res);
          this.banners=res.data.banner.list
          this.recommends=res.data.recommend.list
        })
      },
      getHomeGoods(type){
        const page=this.goods[type].page+1
        getHomeGoods(type,page).then(res => {
         this.goods[type].list.push(...res.data.list)
        this.goods[type].page += 1

        //继续上拉加载更多
        this.$refs.scroll.finishPullUp()
        })
      },



    }
  }
</script>

<style scoped>
  #home{
    height: 100vh;
  }
  .home-nav{
    background-color: var(--color-tint);
    color: #fff;

    position: fixed;
    left: 0;
    right: 0;
    top: 0;
    z-index: 9;
  }
  .tab-control{
    position: sticky;
    top: 44px;
  }
  .content{
    padding-top: 44px;
    overflow: hidden;
    position: absolute;
    top: 44px;
    bottom: 49px;
  }
  /*.content{*/
  /*  margin-top: 44px;*/
  /*  height: calc(100% - 93px);*/
  /*  overflow: hidden;*/
  /*}*/
</style>