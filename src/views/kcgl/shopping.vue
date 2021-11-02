<template>
  <div class="shopping">
    <header>
      <router-link class="back" to="/about/kcgl">返回</router-link>
      <span class="child">购物清单</span>
      <span class="redact"><span v-show="deleteShow" @click="SET_DELETE_SHOW(false)">编辑</span>
      <span v-show="!deleteShow" @click="SET_DELETE_SHOW(true)">完成</span>
      </span>
    </header>

    <ul class="goods_list">
      <li class="goods_item" v-for="item in cartData" :key="item.id">
        <div class="goods_check" :class="{'selector-active':item.select}">
          <input type="checkbox"  @change="setSelectHandle(item)"/>
        </div>
        <div class="goods_img">
          <img :src="item.img" :alt="item.name" />
        </div>
        <div class="goods_wrap">
          <div class="goods_title">
            {{ item.name }}
          </div>
          <div class="goods_price">
            <span>￥</span><b>{{ item.price }}</b>
          </div>
          <div class="goods_people">
            库存<span>{{ item.stock }}165</span>件
          </div>
        </div>
        <div class="goods_num">
          <div class="goods_box" @click="handle(item.id,item.price,item.select,'PLUS')">+</div>
          <div class="goods_box">{{ item.mount }}</div>
          <div class="goods_box" @click="handle(item.id,item.price,item.select,'REDUCE')">-</div>
        </div>
      </li>
    </ul>

    <div class="nav">
      <div class='check'>
        <div class="check-1">
          <div :class="{'selector-active':tSelect}" class="goods_check">
          <input type="checkbox" v-model="tSelect">
          <span>全选</span>
        </div>
        </div>
        <div v-show="deleteShow" >合计:￥<span>{{totalPrice}}</span></div>
      </div>
       
      <div class="clearing" v-show="deleteShow" >结算(<span>{{totalMount}}</span>)</div>
       <div class="clearing" v-show="!deleteShow" @click="sc">删除({{totalMount}})</div>
    </div>
  </div>
</template>
<script>
import {mapState,mapMutations} from "vuex"
export default {
  data(){
    return{
      show:true
    }
  },
  computed:{
    ...mapState(['totalMount','totalPrice','totalSelect','deleteShow']),
    cartData(){
    return this.$store.state.cartData
  },
  tSelect:{
      get(){

        const boo=this.cartData.every(item=>item.select);
         localStorage.setItem('totalSelect',boo);
        return boo;
      },
      set(value){
        console.log(value);
        this.$store.dispatch('setTotalSelect',value)
      }
    },
  },

  created(){
      // console.log(this.$store);
    this.$store.dispatch('setCartData')
  },
  
  methods:{
    ...mapMutations(['SET_DELETE_SHOW','sc']),
    handle(id,price,select,_type){
      this.$store.dispatch('setCart',{
        id,
        price,
        _type
      })

       if (select) {
        if(_type=='PLUS'){
          this.$store.commit('SET_TOTAL',{
            select:select,
            mount:1,
            tPrice:price
          })
        }else if (_type=='REDUCE') {
          this.$store.commit('SET_TOTAL',{
            select:select,
            mount:-1,
            tPrice:-price
          })
        }
      }
    },
    setSelectHandle(item){
      this.$store.dispatch('setSelect',item.id);

      this.$store.dispatch('setTotal',{
        select:item.select,
        tPrice:item.tPrice,
        mount:item.mount
      })

      // 强制刷新，不推荐
      // this.$forceUpdate();
    },



  }
}
</script>

<style lang="scss" scoped>
.shopping {
    width: 100%;
    height: 100%;
    overflow: hidden;
  .check-1{
    margin-top: -19px;
    white-space: nowrap;
  }
  .goods_check{
        position: relative;
    margin: 0.16rem auto 0 auto;
    width: 0.16rem;
    height: 0.16rem;
    border-radius: 50%;
    border: 0.02rem solid palevioletred;
    cursor: pointer;
    float: left;
  }
  .selector-active{
  background-color: #7a45e5;
  border-color: #7a45e5;
}
input{
    width: .16rem;
    height: .16rem;
    opacity: 0;
  }
  header {
    display: flex;
    justify-content: space-around;
    align-items: center;
    height: 0.3rem;
  }
  .goods_list {
    width: 100%;
    overflow: scroll;
    height: 1.9rem;
    .goods_item {
      font-size: 16px;
      display: flex;
      align-items: center;
      justify-content: space-evenly;
      box-shadow: 0 2px 10px pink;
      padding: 10px 10px;
      margin: 20px 0;
      .goods_img {
        width: 0.55rem;
        height: 0.55rem;
        img {
          width: 100%;
          height: 100%;
        }
      }
      .goods_wrap {
        width: 2.2rem;
        padding-left: 0.1rem;
        .goods_title {
          overflow: hidden;
          text-overflow: ellipsis;
          white-space: nowrap;
        }
        .goods_price {
          width: 100%;
          height: 0.2rem;
          line-height: 0.2rem;
          color: pink;
          font-size: 0.14rem;
          font-size: 0.14rem;
        }
        .goods_people {
          width: 100%;
          height: 0.2rem;
          font-size: 0.12rem;
        }
      }
      .goods_num {
        width: 0.2rem;
        height: 0.8rem;
        background-color: pink;
        display: flex;
        flex-direction: column;
        justify-content: space-around;
        font-size: 0.15rem;
        color: white;
        .goods_box {
          width: 0.2rem;
          height: 0.2rem;
          text-align: center;
          line-height: 0.2rem;
        }
      }
    }
  }

  .nav{
    display: flex;
    align-items: center;
    justify-content: space-between;
    height: 0.5rem;
    background: pink;
    width: 100%;
    .check{
      width: 2rem;
    display: flex;
    justify-content: space-around;
    align-items: center;
    span{
      color: red;
    }
    }

    .clearing{
      background: white;
    height: 0.5rem;
    width: 1rem;
    text-align: center;
    line-height: 0.5rem;
    span{
      color: red;
    }
    }

    
  }
}
</style>
