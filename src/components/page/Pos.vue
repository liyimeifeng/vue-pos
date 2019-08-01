<template>
  <div class="pos">
    <el-row>
      <el-col :span="7" class="pos-order" id="order-list">
        <el-tabs>
          <el-tab-pane label="点餐">
            <el-table :data="tableData" border style="width: 100%">
              <el-table-column prop="goodsName" label="商品名称"></el-table-column>
              <el-table-column prop="count" label="数量" width="50"></el-table-column>
              <el-table-column prop="price" label="金额" width="50"></el-table-column>
              <el-table-column label="操作" width="100" fixed="right">
                <template scope="scope">
                  <el-button type="text" size="small" @click="deleSingleGoods(scope.row)">删除</el-button>
                  <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                </template>
              </el-table-column>
            </el-table>
            <div class="total">
              <small>数量：</small>
              {{totalCount}} &nbsp;&nbsp;&nbsp;&nbsp;
              <small>金额：</small>
              {{totalMoney}}元
            </div>
            <div class="div-btn">
              <el-button type="warning">挂单</el-button>
              <el-button type="danger" @click="deleAllGoods">删除</el-button>
              <el-button type="success" @click="checkout">结账</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label="挂单">挂单</el-tab-pane>
          <el-tab-pane label="外卖">外卖</el-tab-pane>

        </el-tabs>
      </el-col>
      <el-col :span="17" class="often-goods">
        <div class="title"> 常用商品</div>
        <div>
          <ul>
            <li v-for="item in oftenGoods" @click="addOrderList(item)">
              <span>{{item.goodsName}}</span>
              <span class="often-goods-price">¥{{item.price}}元</span>
            </li>
          </ul>
        </div>

        <div class="goods-type">
          <el-tabs>
            <el-tab-pane label="汉堡">
              <div>
                <ul class='cookList'>
                  <li v-for="item in type0Goods" @click="addOrderList(item)">
                    <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
                    <span class="foodName">{{item.goodsName}}</span>
                    <span class="foodPrice">￥{{item.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="小食">
              <div>
                <ul class='cookList'>
                  <li v-for="item in type1Goods" @click="addOrderList(item)">
                    <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
                    <span class="foodName">{{item.goodsName}}</span>
                    <span class="foodPrice">￥{{item.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="饮料">
              <div>
                <ul class='cookList'>
                  <li v-for="item in type2Goods" @click="addOrderList(item)">
                    <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
                    <span class="foodName">{{item.goodsName}}</span>
                    <span class="foodPrice">￥{{item.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="套餐">
              <div>
                <ul class='cookList'>
                  <li v-for="item in type3Goods">
                    <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
                    <span class="foodName">{{item.goodsName}}</span>
                    <span class="foodPrice">￥{{item.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>

          </el-tabs>


        </div>
      </el-col>
    </el-row>

  </div>
</template>

<script>
  import axios from "axios";

  export default {
    name: "Pos",
    data: function () {
      return {
        tableData: [],
        oftenGoods: [],
        type0Goods: [],
        type1Goods: [],
        type2Goods: [],
        type3Goods: [],
        totalCount: 0,
        totalMoney: 0,
      }
    },
    methods: {
      addOrderList(goods) {


        //判断商品是否已存在
        let isHava = false;
        for (let i = 0; i < this.tableData.length; i++) {
          if (this.tableData[i].goodsId == goods.goodsId) {
            isHava = true
          }
        }

        //是否已存在
        if (isHava) {
          let arr = this.tableData.filter(o => o.goodsId == goods.goodsId);
          arr[0].count++;
        } else {
          let newGoods = {goodsId: goods.goodsId, goodsName: goods.goodsName, price: goods.price, count: 1};
          this.tableData.push(newGoods);
        }

        this.getAllMoney();
      },

      deleSingleGoods(goods) {
        this.tableData = this.tableData.filter(m => m.goodsId != goods.goodsId);
        this.getAllMoney();
      },

      deleAllGoods() {
        this.tableData = [];
        this.totalMoney =0;
        this.totalCount = 0;
      },

      checkout(){
        if (this.totalCount != 0){
          this.tableData = [];
          this.totalMoney =0;
          this.totalCount = 0;
          this.$message({
            message:"结账成功",
            type:"success"
          })
        } else{

          this.$message.error('购物车为空');
        }


      },

      getAllMoney() {
        this.totalCount = 0;
        this.totalMoney = 0;

        //计算价格和数量
        this.tableData.forEach((element) => {
          this.totalCount += element.count;
          this.totalMoney = this.totalMoney + (element.count * element.price);

        })
      }
    },
    created: function () {

      axios.get("https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods")
        .then(response => {
          console.log(response)
          this.oftenGoods = response.data;
        })
        .catch(error => {
          console.log(error)
        })

      axios.get("https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods")
        .then(response => {
          console.log(response)
          this.type0Goods = response.data[0]
          this.type1Goods = response.data[1]
          this.type2Goods = response.data[2]
          this.type3Goods = response.data[3]

        })
        .catch(error => {

        })
    },
    mounted: function () {
      var orderHeight = document.body.clientHeight;
      console.log(orderHeight);
      document.getElementById("order-list").style.height = orderHeight + "px";
    }

  }
</script>

<style scoped>
  .pos-order {
    background-color: #F9FAFC;
    border-right: 1px solid #C0CCDA;
  }

  .div-btn {
    margin-top: 10px;
  }

  .title {
    background-color: antiquewhite;
    padding: 10px;
    text-align: left;
  }

  .often-goods ul li {
    list-style: none;
    float: left;
    background-color: #FFF;
    padding: 10px;
    margin: 10px;
    border: 1px solid #E5E9F2;
    cursor: pointer;

  }

  .often-goods-price {
    color: #5887FF;
  }

  .goods-type {
    clear: both;
  }

  .cookList li {
    list-style: none;
    width: 23%;
    border: 1px solid #E5E9F2;
    height: auto;
    overflow: hidden;
    background-color: #fff;
    padding: 2px;
    float: left;
    margin: 2px;
    cursor: pointer;

  }

  .cookList li span {

    display: block;
    float: left;
  }

  .foodImg {
    width: 40%;
  }

  .foodName {
    font-size: 18px;
    padding-left: 10px;
    color: brown;

  }

  .foodPrice {
    font-size: 16px;
    padding-left: 10px;
    padding-top: 10px;
  }

  .total {
    font-size: 18px;
    padding: 10px;
    background-color: bisque;
  }
</style>
