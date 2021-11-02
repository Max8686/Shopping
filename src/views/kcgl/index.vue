<template>
  <div class="index">
      
    <header>
      <span>商品列表</span>
      <router-link to="/about/shopping" class="gwc">购物车</router-link>
    </header>

    <div class="flex">
      <ul class="list">
        <li
          class="cate"
          :class="{ 'tab-active': type == item.type }"
          v-for="item in cartType"
          :key="item.id"
          @click="type = item.type"
        >
          {{ item.name }}
        </li>
      </ul>
    </div>

    <ul class="Composite">
      <li
        class="filter-opt price-up"
        :class="{ 'filter-active': status == 'all' }"
        @click="status = 'all'"
      >
        综合排序
      </li>
      <li
        class="filter-opt price-up"
        :class="{ 'filter-active': status == 'sales' }"
        @click="status = 'sales'"
      >
        销量优先
      </li>
      <li
        class="filter-opt filter-price price-up"
        :class="{
          'filter-active': status == 'priceMax' || status == 'priceMin',
        }"
        @click="changePrice"
      >
        价格
      </li>
    </ul>

    <div class="scorll">
      <div class="tj" v-for="item in showGoodsData" :key="item.id">
        <img :src="item.img" alt="" />
        <ul class="xx">
          <li>
            {{ item.name }}
          </li>
          <li><span>￥</span>{{ item.price }}</li>
          <li>{{ item.sales }}付款</li>
        </ul>
        <button class="btn" @click="setCartHandle(item, 'PLUS')">+</button>
      </div>
    </div>
    <router-view></router-view>
  </div>
</template>

<script>
import { mapState } from "vuex";

const cartType = [
  {
    id: 0,
    name: "推荐",
    type: 0,
  },
  {
    id: 1,
    name: "母婴",
    type: 1,
  },
  {
    id: 2,
    name: "鞋包饰品",
    type: 2,
  },
  {
    id: 3,
    name: "食品",
    type: 3,
  },
  {
    id: 4,
    name: "数码家电",
    type: 4,
  },
  {
    id: 5,
    name: "居家百货",
    type: 5,
  },
];
let boo = true;
export default {
  data() {
    return {
      cartType,
      type: 0,
      status: "all",
    };
  },

  computed: {
    ...mapState(["goodsData"]),

    showGoodsData() {
      let data = "";
      if (this.type == 0) {
        // return this.$store.state.goodsData;
        data = this.goodsData.map((item) => item);
      } else {
        // return  this.goodsData.filter(item=>item.type==this.type);
        data = this.goodsData.filter((item) => item.type == this.type);
        // console.log(data);
      }
      // console.log(data);
      switch (this.status) {
        case "all":
          return data;

        case "sales":
          console.log(123);
          return data.sort((a, b) => {
            return b.sales - a.sales;
          });
        case "priceMax":
          return data.sort((a, b) => b.price - a.price);
        case "priceMin":
          return data.sort((a, b) => a.price - b.price);
      }
      return data;
    },
  },

  created() {
    this.$store.dispatch("setData");
  },
  methods: {
    changePrice() {
      if (boo) {
        this.status = "priceMax";
        boo = false;
      } else {
        this.status = "priceMin";
        boo = true;
      }
    },

    setCartHandle(item, _type) {
      this.$store.dispatch("setCart", {
        id: item.id,
        img: item.img,
        name: item.name,
        price: item.price,
        stock: item.stock,
        _type,
      });
    },
  },
};
</script>

<style lang="scss" scoped>
.index {
  width: 100%;
  height: 100%;
  overflow: hidden;
  header {
    height: 0.3rem;
    display: flex;
    align-items: center;
    justify-content: space-around;
    .gwc {
      position: absolute;
      float: right;
      top: 56px;
      right: 189px;
    }
  }

  .flex {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: space-between;
    background: pink;
    // 清除滚动条
    .list::-webkit-scrollbar {
      display: none;
    }
    .list {
      // 文本强制不换行
      // white-space: nowrap;
      display: flex;
      overflow-x: scroll;
      li {
        width: 0.8rem;
        height: 53px;
        flex-shrink: 0;
        // display: inline-block;
        line-height: 50px;
        text-align: center;
        padding: 0 7px;
        font-size: 13px;
      }
      .tab-active {
        background-color: white;
      }
    }
  }

  .Composite {
    display: flex;
    align-items: center;
    justify-content: space-around;
    height: 0.3rem;
    color: white;
    border-bottom: solid 1px pink;
  }

  .scorll {
    overflow-y: scroll;
    height: 70%;
    .tj {
      padding: 10px 0;
      display: flex;
      background: white;
      justify-content: space-around;
      align-items: flex-end;
      margin-bottom: 20px;
      align-items: center;
      box-shadow: 0 2px 10px pink;
      img {
        width: 80px;
        height: 80px;
        float: left;
      }
      .xx {
        width: 2rem;
        li:nth-child(1) {
          // 老弹性盒
          display: -webkit-box;
          -webkit-box-orient: vertical;
          // 多行显示  最大两行
          -webkit-line-clamp: 2;
          // 超出文本隐藏
          overflow: hidden;
        }
        li:nth-child(2) {
          color: pink;
          font-weight: 600;
        }
        li {
          margin-top: 5px;
        }
      }
      .btn {
        background: pink;
        text-align: center;
        font-size: 25px;
        font-weight: 700;
        color: white;
        width: 0.2rem;
        border: none;
      }
    }
  }
  .filter-active {
    color: pink;
  }
}
</style>
