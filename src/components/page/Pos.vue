<template>
  <div class="pos" style="height:100%">
    <div style="height:100%">
      <el-row style="height:100%">
        <el-col :span="7" style="padding-top:10px;height:100%;border-right:2px solid #eee;">
          <el-tabs>
            <el-tab-pane label="点餐">
              <el-table :data="tableData" border show-summary style="width: 100%">
                <el-table-column prop="goodsName" label="商品"></el-table-column>
                <el-table-column prop="count" label="量" width="50"></el-table-column>
                <el-table-column prop="price" label="金额" width="70"></el-table-column>
                <el-table-column label="操作" width="100" fixed="right">
                  <template slot-scope="scope">
                    <el-button type="text" size="mini" @click="delOrderList(scope.row)">删除</el-button>
                    <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                  </template>
                </el-table-column>
              </el-table>
              <div style="margin-top:10px;">
                <el-button type="warning">挂单</el-button>
                <el-button type="danger" @click="delAllGoods">删除</el-button>
                <el-button type="success" @click="checkout">结账</el-button>
              </div>
            </el-tab-pane>
            <el-tab-pane label="挂单">挂单</el-tab-pane>
            <el-tab-pane label="外卖">外卖</el-tab-pane>
          </el-tabs>
        </el-col>
        <el-col :span="17" style="padding-top:10px;height:100%;">
          <div class="often-goods">
            <div class="title">常用商品</div>
            <div class="often-goods-list">
              <ul>
                <li v-for="(goods,index) in oftenGoods" :key="index" @click="addOrderList(goods)">
                  <span>{{goods.goodsName}}</span>
                  <span class="o-price">￥{{goods.price}}元</span>
                </li>
              </ul>
            </div>
          </div>
          <div class="goods-type">
            <el-tabs :tab-position="tabPosition">
              <el-tab-pane label="汉堡">
                <ul class="cookList">
                  <li v-for="(goods,index) in type0Goods" :key="index" @click="addOrderList(goods)">
                    <span class="foodImg">
                      <img :src="goods.goodsImg" width="100%">
                    </span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </el-tab-pane>
              <el-tab-pane label="小食">
                <ul class="cookList">
                  <li v-for="(goods,index) in type1Goods" :key="index" @click="addOrderList(goods)">
                    <span class="foodImg">
                      <img :src="goods.goodsImg" width="100%">
                    </span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </el-tab-pane>
              <el-tab-pane label="饮料">
                <ul class="cookList">
                  <li v-for="(goods,index) in type2Goods" :key="index" @click="addOrderList(goods)">
                    <span class="foodImg">
                      <img :src="goods.goodsImg" width="100%">
                    </span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </el-tab-pane>
              <el-tab-pane label="套餐">
                <ul class="cookList">
                  <li v-for="(goods,index) in type3Goods" :key="index" @click="addOrderList(goods)">
                    <span class="foodImg">
                      <img :src="goods.goodsImg" width="100%">
                    </span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </el-tab-pane>
            </el-tabs>
          </div>
        </el-col>
      </el-row>
    </div>
  </div>
</template>
<script>
import { get, post } from "@/plugins/http.js";
export default {
  name: "Pos",
  methods: {
    //添加订单列表的方法
    addOrderList(goods) {
      this.totalCount = 0; //汇总数量清0
      this.totalMoney = 0;
      let isHave = false;
      //判断是否这个商品已经存在于订单列表
      for (let i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].goodsId == goods.goodsId) {
          isHave = true; //存在
        }
      }
      //根据isHave的值判断订单列表中是否已经有此商品
      if (isHave) {
        //存在就进行数量添加
        let arr = this.tableData.filter(o => o.goodsId == goods.goodsId);
        let price = goods.price / arr[0].count;
        arr[0].count++;
        arr[0].price = arr[0].count * price;
        //console.log(arr);
      } else {
        //不存在就推入数组
        let newGoods = {
          goodsId: goods.goodsId,
          goodsName: goods.goodsName,
          price: goods.price,
          count: 1
        };
        this.tableData.push(newGoods);
      }
    },
    delOrderList(goods) {
      //   let arr = this.tableData.filter(o => o.goodsId == goods.goodsId);
      let index = -1;
      let arr = this.tableData.filter(function(o, i) {
        if (o.goodsId == goods.goodsId) {
          index = i;
          return true;
        }
        return false;
        // return o.goodsId == goods.goodsId && (index = i);
      });
      console.log(index);
      let price = goods.price / arr[0].count;
      arr[0].count--;
      arr[0].price = arr[0].count * price;
      if (arr[0].count == 0) {
        this.tableData.splice(index, 1);
      }
    },
    delAllGoods() {
      this.tableData = [];
    },
    checkout() {
      if (this.tableData.length != 0) {
        this.tableData = [];
        this.$message({
          message: "结账成功，感谢你又为店里出了一份力!",
          type: "success"
        });
      } else {
        this.$message.error("不能空结。老板了解你急切的心情！");
      }
      /*   post("", {})
        .then(res => {
          if (this.totalCount != 0) {
            this.tableData = [];
            this.$message({
              message: "结账成功，感谢你又为店里出了一份力!",
              type: "success"
            });
          } else {
            this.$message.error("不能空结。老板了解你急切的心情！");
          }
        })
        .catch(function(reason) {
          console.log(reason);
        }); */
    }
  },
  data() {
    return {
      tabPosition: "top",
      tableData: [],
      oftenGoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: []
    };
  },
  created() {
    get(
      "https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods"
    )
      .then(res => {
        this.oftenGoods = res;
      })
      .catch(function(reason) {
        console.log(reason);
      });
    get(
      "https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods"
    )
      .then(res => {
        this.type0Goods = res[0];
        this.type1Goods = res[1];
        this.type2Goods = res[2];
        this.type3Goods = res[3];
      })
      .catch(function(reason) {
        console.log(reason);
      });
  }
};
</script>
<style scoped>
.title {
  height: 20px;
  border-bottom: 1px solid #d3dce6;
  background-color: #fff;
  padding: 9px;
}
.often-goods-list {
  overflow: hidden;
  background-color: #f9fafc;
}
.often-goods-list ul li {
  list-style: none;
  float: left;
  border: 1px solid #e5e9f2;
  padding: 10px;
  margin: 5px;
  background-color: #fff;
}
.o-price {
  color: #58b7ff;
}
.cookList li {
  list-style: none;
  width: 23%;
  border: 1px solid #e5e9f2;
  height: auot;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
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
</style>