<template>
  <div>
    <x-header :left-options="{backText: ''}" style="background: transparent;position: fixed;" v-on:click="$router.back()"></x-header>
    <blur :blur-amount=0 :url="url" style="height:220px">

      <div style="border-radius: 2px;width: 100%;">
        <div class="m-buttom" >营养价值分析</div>
        <div class="m-buttom1"></div>
        <ul class="me_show">
          <li><p>{{attentions}}</p></li>
          <li @click="GoNutritionDetail"><p>{{editedate}}</p></li>
        </ul>
      </div>
    </blur>
    <div>
      <panel :list="list" @on-click-item="GoRankingdetails" >
      </panel>
    </div>
  </div>
</template>

<script>
  import { XHeader, Flexbox, FlexboxItem, Blur, Masker, Group, Grid, GridItem, Cell, Panel } from 'vux'
  import img from '../../../assets/images/7.png'
  import img1 from '../../../assets/images/8.png'
  import img2 from '../../../assets/images/9.png'
  import img3 from '../../../assets/images/10.png'
  import { Indicator } from 'mint-ui'

  import { mapState } from 'vuex'
  export default {
    components: {
      XHeader,
      Blur,
      Flexbox,
      FlexboxItem,
      Masker,
      Group,
      Grid,
      GridItem,
      Cell,
      Panel
    },
    data () {
      return {
        // 修改panel源码文件来更改字段名
        list: [{
          src: img1,
          title: 'NO.1',
          desc: '水果紫米粥',
          dishId: 1
        }, {
          src: img2,
          title: 'NO.2',
          desc: '营养*早餐',
          dishId: 2
        }, {
          src: img3,
          title: 'NO.3',
          desc: '营养*早餐',
          dishId: 3
        }],
        url: img,
        attentions: '营养菜品排行',
        editedate: '点击了解详情'
      }
    },
    created () {
      this.get()
    },
    methods: {
      get () {
        Indicator.open({
          text: '加载中...',
          spinnerType: 'fading-circle'
        })
        this.$store.dispatch('getNutritionDishes', {
          uri: '/rank'
        }).then(() => {
          Indicator.close()
          let data = this.$store.getters.getNutritionDishes
          var datalist = []
          if (data.code !== -1) {
            data = data.data
            // 装数据
            for (let i = 0; i < 3; i++) {
              datalist.push({
                src: data[i].dishImage,
                title: 'NO.' + (parseInt(i) + 1),
                desc: data[i].dishName,
                dishId: data[i].dishId
              })
            }
            this.$set(this, 'list', datalist)
          }
        })
      },
      GoNutritionDetail () {
        this.$router.push({name: 'NutritionDetail'})
      },
      GoRankingdetails (params) {
        this.$router.push({name: 'dishesDetail', params: {dishId: params.dishId}})
      },
      gets () {
        this.$store.dispatch('getRankings', {
          params: {
          }
        }).then(() => {
          if (this.$store.getters.getRankings.code !== -1) {
            console.info(this.$store.getters.getRankings.data.page.list)
            this.$set(this, 'list', this.$store.getters.getRankings.data.page.list)
          }
        })
      }
    },
    mounted () {
      // 进入页面的钩子函数
    },
    computed: mapState([
      'ranking'
    ])
  }
</script>

<style scoped>
  .m-buttom {
    display: block;
    position: absolute;
    width: 70%;
    margin-left: 15%;
    cursor: pointer;
    border-radius: 2px;
    height: 40%;
    background: #f3f3f5;
    opacity: 0.5;
    top:20%;
    margin-bottom: 25%;
    text-align: center;
    line-height: 100px;
    color: #000;
    font-size:24px;
    font-weight: bold;
  }
  .m-buttom1 {
    display: block;
    position: absolute;
    width: 100%;
    background-size: cover;
    background-position: center center;
    cursor: pointer;
    border-radius: 2px;
    height: 50px;
    background: #f3f3f5;
    opacity: 0.8;
    bottom: -5px;
  }
  .me_show{
    display: flex;
    width: 100%;
    height:40px;
    position: absolute;
    bottom: 0px;
    color:#000;
  }
  .me_show li{
    float: left;
    padding-left:20px;
  }
  .me_show li:nth-child(1){
    line-height:40px;
    padding-right: 20px;
    font-size:20px;
  }
  .me_show li:last-child{
    position: absolute;
    right: 0;
    line-height:40px;
    padding-right: 20px;
    font-size:15px;
  }
</style>
