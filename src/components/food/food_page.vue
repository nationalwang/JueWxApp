<template>
  <div style="margin-top: -20px;"  @touchstart="getY" @touchend="getMore">
      <!--<load-more  v-if="loadmore" tip="正在加载"></load-more>-->
      <!--<JueLoading v-if="jueloading"></JueLoading>-->

    <mt-loadmore :top-method="loadTop" :bottom-method="loadBottom" :bottom-all-loaded="allLoaded" topLoadingText="小蕨努力加载中..." ref="loadmore">

      <divider style="margin-top:12%;font-size:16px;background-color: #fff;">
        看看大家都在吃什么
      </divider>
        <div v-for="item in list">
          <div  style="background-color: #fff;padding:2% 2%;
          overflow: hidden;height: 180px;position: relative;">
            <div class="avatar" v-on:click="GoArticle(item)">
              <img class="avatarimg" :src="item.headImgUrl" >
            </div>
            <p class="f-name" v-on:click="GoArticle(item)">{{item.username}}</p>
            <p class="f-time">{{item.addTime}}</p>
            <p class="f-title" v-on:click="GoArticle(item)">{{item.title}}</p>
            <!--<img class="a-img" v-if="item.collectionId === null" v-on:click="collect(item.articleId)"-->
                 <!--src="../../assets/images/heart_default.png" style="height: 40px; width: 40px; padding: 5px"/>-->
            <!--<img class="a-img" v-else v-on:click="cancelCollect(item.collectionId)"-->
                 <!--src="../../assets/images/heart_select.png" style="height: 40px; width: 40px; padding: 5px"/>-->
            <div class="photo1" v-on:click="GoArticle(item)" v-for="i in item.recImageList"
                 v-if="item.recImageList.length===1">
              <!--<img :src="i">-->
              <div :style="{backgroundImage: 'url('+i+')'}"></div>
            </div>
            <div class="photo2" v-on:click="GoArticle(item)"
                 v-if="item.recImageList.length===2">
              <!--<img :src="i">-->
              <div  v-for="i in item.recImageList"
                    :style="{backgroundImage: 'url('+i+')'}" class="photo2div"></div>
            </div>
            <div class="photo3" v-on:click="GoArticle(item)"
                 v-if="item.recImageList.length===3">
              <div  v-for="i in item.recImageList"
                    :style="{backgroundImage: 'url('+i+')'}" class="photo3div"></div>
            </div>
            <div class="photo4" v-on:click="GoArticle(item)" v-for="i in item.recImageList"
                 v-if="item.recImageList.length===4">
              <img :src="i">
            </div>
          </div>
        </div>
    </mt-loadmore>
  </div>
</template>

<script>
  let startY = 0
  let endY = 0
  import { Divider, Rater, LoadMore } from 'vux'
//  import { JueLoading } from '../../loading/index.js'
  import { mapState } from 'vuex'
  import { Indicator, Toast } from 'mint-ui'
  import time from '../../utils/helpers/timeLite'

  export default {
    components: {
      Divider,
      Rater,
      LoadMore,
      Toast
//      ...JueLoading
    },
    computed: mapState([
      'food'
    ]),
    data () {
      return {
        current: 1,
        allLoaded: false,
//        jueloading: false,
        list: []
//        list: [{
//          headImgUrl: ava,
//          username: '蕨菜团队',
//          addTime: '2017-5-26',
//          title: '好的食物应该大家分享，今天的美食推荐给大家~',
//          recImageList: [food1, food2, food3],
//          data1: 0,
//          title1: '营养早餐',
//          title2: '美味烧烤',
//          title3: '鱼片寿司',
//          collectionId: '',
//          articleId: ''
//        }, {
//          headImgUrl: ava,
//          username: '蕨菜团队',
//          addTime: '2017-5-26',
//          title: '好的食物应该大家分享，今天的美食推荐给大家~',
//          recImageList: [food1, food2, food3],
//          data1: 0,
//          title1: '营养早餐',
//          title2: '美味烧烤',
//          title3: '鱼片寿司',
//          collectionId: '',
//          articleId: ''
//        }]
      }
    },
    mounted () {
      this.gets()
    },
    methods: {
      gets () {
        Indicator.open({
          text: '加载中...',
          spinnerType: 'triple-bounce'
        })
        this.$store.dispatch('getArticles', {
          params: {
            pageNum: this.current,
            pageSize: 5
          }
        }).then(() => {
          Indicator.close()
          console.info(this.$store.getters.getArticles)
          let data = this.$store.getters.getArticles
          if (data.code !== -1) {
            data = data.data
            for (let i = 0; i < data.page.list.length; i++) {
              data.page.list[i].addTime = time.getDate(data.page.list[i].addTime)
            }
            this.$set(this, 'list', data.page.list)
            console.info(this.list)
          }
        })
      },
      getY (e) {
        startY = e.changedTouches[0].clientY
      },
      getMore (e) {
        endY = e.changedTouches[0].clientY
        if (startY < endY) {
//          this.jueloading = true
//          let t = setInterval(function () {
//            this.jueloading = true
//            console.log(this.jueloading)
//          }, 2000)
          console.log('加载中')
        }
        if (startY > endY) {
//          this.jueloading = false
          this.loadmore = true
        }
      },
      loadTop () {
        this.current = 1
        this.gets()
        this.allLoaded = false// 若数据已全部获取完毕
        this.$refs.loadmore.onTopLoaded()
      },
      loadBottom () {
        this.current++
        Indicator.open({
          text: '加载中...',
          spinnerType: 'triple-bounce'
        })
        this.$store.dispatch('getArticles', {
          params: {
            pageNum: this.current,
            pageSize: 5
          }
        }).then(() => {
          Indicator.close()
          let data = this.$store.getters.getArticles
          if (data.code !== -1) {
            data = data.data
            for (let i = 0; i < data.page.list.length; i++) {
              data.page.list[i].addTime = time.getDate(data.page.list[i].addTime)
              this.list.push(data.page.list[i])
            }
            console.info(this.list)
            if (data.page.lastPage === this.current) {
              this.allLoaded = true// 若数据已全部获取完毕
            }
          }
        })
        this.$refs.loadmore.onBottomLoaded()
      },
      cancelCollect (collectionId) {
        this.$store.dispatch('cancelCollections', {
          params: {
            collectionId: collectionId
          }
        }).then(() => {
          this.gets()
          Toast('取消收藏成功')
        })
      },
      collect (articleId) {
        this.$store.dispatch('setCollections', {
          data: {
            businessDishId: articleId,
            collectionType: 3
          }
        }).then(() => {
          this.gets()
          Toast('收藏成功')
        })
      },
      load (uuid) {
        const _this = this
        setTimeout(function () {
          _this.$broadcast('pulldown:reset', uuid)
        }, 2000)
      },
      GoArticle (item) {
        this.$router.push({name: 'article', params: {articleId: item.articleId}})
      }
    }
  }
</script>

<style scoped lang="less">
  .avatar{
    display: inline-block;
    width:15%;
    overflow: hidden;
  }
  .avatar img.avatarimg{
    width: 100%;
    border-radius: 50%;
  }
  .f-name{
    position: absolute;
    top:4%;
    left:20%;
    display:inline-block;
    font-size:14px;
    color:#777;
  }
  .f-time{
    position: absolute;
    top:4%;
    left:62%;
    font-size:10px;
    color:#777;
  }
  .f-title{
    position: absolute;
    font-size:12px;
    top:20%;
    left:20%;
    color:#666;
  }
  .photo3{
    width: 100%;
  }
  /*.photo3 img{*/
    /*width: 30%;*/
    /*float: left;*/
    /*margin-top:1%;*/
    /*margin-left:3%;*/
  /*}*/
  .photo3div{
    float: left;
    background-size: cover;
    width: 30%;
    margin-left: 5px;
    height: 100px;
  }
  .photo2{
    width: 100%;
  }
  .photo2div{
    float: left;
    background-size: cover;
    width: 48%;
    margin-left: 5px;
    height: 100px;
  }
  .photo1{
    width: 100%;
    height:100px;
  }
  .photo1>div{
    height: 100%;
    width: 100%;
    background-size: cover;
  }

  .photo4{
    width: 100%;
  }
  .photo4 img{
    width: 20%;
    float: left;
    margin-top:1%;
    margin-left:3%;
  }
  .a-img{
    display:inline-block;
    font-size:14pt;
    position: absolute;
    right:5%;
    padding: 7px;
  }
</style>
<style>
  .vux-rater {
    text-align: left;
    display: inline-block;
    line-height: normal;
  }
  .vux-rater a {
    display: inline-block;
    text-align: center;
    cursor: pointer;
    color: #ccc;
  }
  .vux-rater a:last-child {
    padding-right: 2px!important;
    margin-right: 0px!important;
  }
  .vux-rater a:hover {
    color: #ffdd99;
  }
  .vux-rater a.is-disabled {
    color: #ccc !important;
    cursor: not-allowed;
  }
  .vux-rater-box {
    position: relative;
  }
  .vux-rater-inner {
    position: relative;
    display: inline-block;
  }
  .vux-rater-outer {
    position: absolute;
    left: 0;
    top: 0;
    display: inline-block;
    overflow: hidden;
  }
</style>
