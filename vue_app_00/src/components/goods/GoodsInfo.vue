<template>
    <div class="app-goodsinfo">
        <!--轮播图-->
        <swiper-box :list="list"></swiper-box>
          <!--商品信息-->
        <div class="mui-card">
          <div>
            <img :src="info.img_url" alt="">
          </div>
          <div class="mui-card-header">
              {{info.name}}
          </div>
          <div class="mui-card-content">
              <div class="mui-card-inner">
                 <div class="price">
                    市场价:<del>{{info.price}}</del>
                    销售价:<span class="now">
                    {{info.price/2}}</span>
                 </div>
                 购买数量:
                <div class="mui-numbox" data-numbox-min='1' data-numbox-max='9'>
                  <button class="mui-btn mui-btn-numbox-minus" type="button" @click="goodsSub">-</button>
                  <input id="test" class="mui-input-numbox" type="number" value="1" v-model="val" />
                  <button class="mui-btn mui-btn-numbox-plus" type="button" @click="goodsAdd">+</button>
                </div>
                <div class="total">
                  小计：{{(info.price/2*(2))}}
                </div>
              </div>
          </div>
          <div  class="mui-card-footer">
              <mt-button type="primary" size="small">立即购买</mt-button>
              <mt-button type="primary" size="small" @click="addCart">加入购物车</mt-button>
          </div>      
        </div>
          <!--商品参数-->
    </div>  
</template>
<script> 
    import {Toast} from "mint-ui";
    //1:引入子组件
    import swipe from "../sub/swiper.vue";
    //2:引入mui 组件js文件
    //import mui from "../../lib/mui/js/mui.js";
    export default {
        components:{//添加组件"swiper-box"
            "swiper-box":swipe
        },
        created() {
            //console.log("list组件参数:"+this.id);
            this.getImageList();//轮播图
            this.getGoodsInfo();//商品信息
        },
        data(){
            return  {
              id:this.$route.params.id,
              list:[],
              val:1,
              info:{}
            }
          },
          methods:{
            getGoodsInfo(){
              //1:获取参数 id
              var id = this.id;
              //2:发送ajax请求获取商品信息
              var url = "http://127.0.0.1:3000/getProduct";
              url +="?id="+id;
              this.axios.get(url).then(result=>{
                  console.log(result.data.data);
                  this.info = result.data.data;
              });
            },
            addCart(){
              //1:获取参数 uid=1 pid count price
              var uid = 1;//当前登录用户
              var pid = this.id;//商品编号
              var count = this.val;//商品数量
              var price =  99;
              //2:发送ajax请求将数据发送服务器
              var url = "http://127.0.0.1:3000";
              url += "/addCart";
              this.axios.get(url,{
                params:{
                  uid:uid,
                  pid:pid,
                  count:count,
                  price:price
                }
              }).then(result=>{
                  if(result.data.code > 0){
                    //商品添加成功
                    //修改全局共享数据 cartCount
                    this.$store.commit("increment"),
                    Toast(result.data.msg);
                  }else{
                    Toast(result.data.msg);
                  }
              });
            },
            goodsSub(){
              if(this.val > 1){
                 this.val--;
              }
            },
            goodsAdd(){
              if(this.val <= 998){
                  this.val++;
              }
            },
            getImageList(){
               var url = "http://127.0.0.1:3000/";
               url +="getImages";
               this.axios.get(url).then(result=>{
                 console.log(result.data);
                 this.list = result.data;
               });
               //console.log(this.list);?????
             } 
          }
        }  
</script>
<style>
  .app-goodsinfo .mui-card img{
    width: 100%;
    height: 100%;
  }
</style>