<template>
<div class="pos">
  <el-row class="flex pos_main">
    <el-col :span='7' class="pos_order">
      <el-tabs>
        <el-tab-pane label="点餐">
          <el-table :data="tableData" border style="height:100%;">
            <el-table-column prop="goodsName" label="商品名称"></el-table-column>
            <el-table-column prop="price" label="金额" width="60"></el-table-column>
            <el-table-column prop="count" label="数量" width="60"></el-table-column>
            <el-table-column label="操作" width="100" fixed="right">
              <template scope="scope">
                <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
              </template>
            </el-table-column>
          </el-table>
          <div class="flex goods_sum">
            <p><small>数量：</small>{{this.totalCount}}</p></small>
            <p><small>金额：</small>{{this.totalMoney }}<small>元</small></p>
          </div>
          <div class="div_btn">
            <el-button type="warning">挂单</el-button>
            <el-button type="danger" @click="delAllgoods">删除</el-button>
            <el-button type="success" @click="checkout">结账</el-button>
          </div>
        </el-tab-pane>
        <el-tab-pane label="挂单">

        </el-tab-pane>
        <el-tab-pane label="外卖">

        </el-tab-pane>
      </el-tabs>
    </el-col>
    <el-col :span="17" class="pos_goods">
      <div class="often_goods">
        <div class="title">常用商品</div>
        <div class="often_goods_list">
          <ul>
            <li @click="addOrderList(goods)" class="often_good" v-for="(goods,index) of oftenGoods" :key="goods.goodsId">
              <span>{{goods.goodsName}}</span>
              <span class="o_price">￥{{goods.price}}元</span>
            </li>
          </ul>
        </div>
      </div>
      <div class="goods_type">
        <el-tabs>
          <el-tab-pane :label="typexGoods.typeName" v-for="(typexGoods,i) of typeGoods" :key="i">
            <div>
              <ul class="cookList">
                <li @click="addOrderList(item)" v-for="(item,index) of typexGoods.typexGoods" :key="item.goodsId">
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
import axios from 'axios'
export default {
  name: 'pos',
  props: {},
  data() {
    return {
      tableData: [],
      oftenGoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: [],
      typeGoods: [],
      totalMoney: 0,
      totalCount: 0
    }
  },
  methods: {
    addOrderList(goods) {
      //判断商品是否存在于订单列表中
      let isHave = false;
      for (let i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].goodsId == goods.goodsId) {
          isHave = true;
        }
      }
      //根据判断，编写业务逻辑
      if (isHave) {
        //改变列表中商品的数量
        let arr = this.tableData.filter(a => a.goodsId == goods.goodsId)
        arr[0].count++
      } else {
        let newGoods = {
          goodsId: goods.goodsId,
          goodsName: goods.goodsName,
          price: goods.price,
          count: 1
        }
        this.tableData.push(newGoods)
      }
      this.getAllMoney()
    },
    delSingleGoods(goods) {
      this.tableData = this.tableData.filter(a => a.goodsId != goods.goodsId)
      this.getAllMoney()
    },
    //模拟结账
    checkout() {
      if (this.totalCount != 0) {
        this.tableData = []
        this.totalCount = 0
        this.totalMoney = 0
        this.$message({
          message: '结账成功，感谢你又为店里出了一份力！',
          type: "success"
        })
      } else {
        this.$message.error('不能空结，老板了解你急切的心情！')
      }
    },
    //得到汇总数量和金额
    getAllMoney() {
      this.totalCount = 0
      this.totalMoney = 0
      if (this.tableData) {
        //汇总金额数量
        this.tableData.forEach((element) => {
          this.totalCount += element.count
          this.totalMoney += element.price * element.count
        })
      }
    },
    delAllgoods() {
      this.tableData = []
      this.totalCount = 0
      this.totalMoney = 0
    }
  },
  created() {
    axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods')
      .then(reponse => {
        this.oftenGoods = reponse.data
      }).catch(error => {
        console.log(error)
        console.log('error')
      })
    axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods')
      .then(reponse => {
        console.log(reponse)
        this.type0Goods = reponse.data[0]
        this.type1Goods = reponse.data[1]
        this.type2Goods = reponse.data[2]
        this.type3Goods = reponse.data[3]
        this.typeGoods = [{
          typeName: '汉堡',
          typexGoods: this.type0Goods
        }, {
          typeName: '小食',
          typexGoods: this.type1Goods
        }, {
          typeName: '饮料',
          typexGoods: this.type2Goods
        }, {
          typeName: '套餐',
          typexGoods: this.type3Goods
        }]
      }).catch(error => {
        console.log(error)
        console.log('error')
      })
  }
}
</script>

<style scoped lang="scss">
li {
    list-style: none;
}
.flex {
    display: flex;
    justify-content: center;
    align-items: center;
}
.pos {
    height: 100%;
    .pos_main {
        height: 100%;
        .pos_order {
            background-color: #F9FAFC;
            border-right: 1px solid #C0CCDA;
            height: 100%;
            .div_btn {
                margin-top: 10px;
            }
        }
        .pos_goods {
            height: 100%;
            .often_goods {
                .title {
                    height: 20px;
                    border-bottom: 1px solid #D3DCE6;
                    background-color: #F9FAFC;
                    padding: 10px;
                    text-align: left;
                }
            }
            .goods_type {
                clear: both;
            }
        }
    }
}
.often_good {
    float: left;
    border: 1px solid #D3DCE6;
    padding: 10px;
    margin: 10px;
    background-color: #fff;
    cursor: pointer;
    .o_price {
        color: #58B7FF;
    }
}
.cookList li {
    list-style: none;
    width: 23%;
    border: 1px solid #E5E9F2;
    height: auot;
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
.goods_sum {
    justify-content: space-around;
    background-color: #fff;
    padding: 10px;
    border-bottom: 1px solid #D3DCE6;
}
.list_btn {
    font-size: 10px;
    padding: 2px;
}
</style>
