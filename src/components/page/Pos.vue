<template>
  <div class="pos">
    <el-row>
      <el-col :span="7" class="pos-order" id="order-list">
        <el-tabs>
          <el-tab-pane label="点餐">
            <el-table :data="dataName" border style="width:100%">
              <el-table-column align="center" prop="goodsName" label="商品名称"></el-table-column>
              <el-table-column align="center" prop="count" label="数量" width="70"></el-table-column>
              <el-table-column align="center" prop="price" label="金额" width="70"></el-table-column>
              <el-table-column align="center" label="操作" width="100" fixed="right">
                <template scope="scope">
                  <el-button type="text" size="small" @click="delOrderList(scope.row)">删除</el-button>
                  <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                </template>
              </el-table-column>
            </el-table>
            <div class="title-list">
              <small>数量：</small>{{totalCount}}&nbsp;&nbsp;&nbsp;&nbsp;<small>金额：</small>{{totalPrice}}
            </div>
            <div class="div-btn">
              <el-button type="warning">挂单</el-button>
              <el-button type="danger" @click="delAllList()">删除</el-button>
              <el-button type="success" @click="checkOut()">结账</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label="挂单">
            2
          </el-tab-pane>
          <el-tab-pane label="外卖">
            3
          </el-tab-pane>
        </el-tabs>
      </el-col>
      <el-col :span="17">
        <div class="often-goods">
          <div class="title">常用商品</div>
          <div class="often-list">
            <ul>
              <li v-for="item in dataOften" class="animated pulse" @click="addOrderList(item)">
                <span>{{item.goodsName}}</span>
                <span class="oprice">￥{{item.price}}元</span>
              </li>
            </ul>
          </div>
        </div>

        <div class="goodsType">
          <el-tabs>
            <el-tab-pane label="汉堡">
              <div>
                <ul class="foodCook">
                  <li v-for="foods in foodsDate1" class="animated bounceInRight" @click="addOrderList(foods)">
                    <span class="foodsImg">
                      <img :src="foods.goodsImg" width="100%">
                    </span>
                    <span class="foodsName">{{foods.goodsName}}</span>
                    <span class="foodsPrice">￥{{foods.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="小食">
              <div>
                <ul class="foodCook">
                  <li v-for="foods in foodsDate2" class="animated bounceInDown" @click="addOrderList(foods)">
                    <span class="foodsImg">
                      <img :src="foods.goodsImg" width="100%">
                    </span>
                    <span class="foodsName">{{foods.goodsName}}</span>
                    <span class="foodsPrice">￥{{foods.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="饮料">
              <div>
                <ul class="foodCook">
                  <li v-for="foods in foodsDate3" class="animated bounceInLeft" @click="addOrderList(foods)">
                    <span class="foodsImg">
                      <img :src="foods.goodsImg" width="100%">
                    </span>
                    <span class="foodsName">{{foods.goodsName}}</span>
                    <span class="foodsPrice">￥{{foods.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="套餐">
              <div>
                <ul class="foodCook">
                  <li v-for="foods in foodsDate4" class="animated bounceInUp" @click="addOrderList(foods)">
                    <span class="foodsImg">
                      <img :src="foods.goodsImg" width="100%">
                    </span>
                    <span class="foodsName">{{foods.goodsName}}</span>
                    <span class="foodsPrice">￥{{foods.price}}元</span>
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
import axios from 'axios';
export default {
  name: 'pos',
  data(){
    return {
      dataName:[],
      dataOften:[],
      foodsDate1:[],
      foodsDate2:[],
      foodsDate3:[],
      foodsDate4:[],
      totalCount:0,
      totalPrice:0,
    }
  },
  created(){
    axios.get('http://jspang.com/DemoApi/oftenGoods.php')
    .then(reponse=>{
      this.dataOften = reponse.data;
    })
    .catch(error=>{
      alert('网络错误，无法访问')
    });

    axios.get('http://jspang.com/DemoApi/typeGoods.php')
    .then(reponse=>{
      this.foodsDate1 = reponse.data[0];
      this.foodsDate2 = reponse.data[1];
      this.foodsDate3 = reponse.data[2];
      this.foodsDate4 = reponse.data[3];
    })
    .catch(error=>{
      alert('网络错误，无法访问')
    });
  },
  mounted(){
    let orderHeight = document.body.clientHeight;
    document.getElementById('order-list').style.height = orderHeight + 'px';
  },
  methods:{
   addOrderList(goods){
     this.totalCount = 0;
     this.totalPrice = 0;
     let isHave = false;
     for(let i of this.dataName){
       if(i.goodsId == goods.goodsId){
         isHave = true
       }
     }
     if(isHave){
       let arr = this.dataName.filter(o=>o.goodsId == goods.goodsId);
       arr[0].count++;
     }else{
       let newData = {goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1};
       this.dataName.push(newData)
     }
     this.getAllMethod();
   },
   delOrderList(goods){
     this.dataName = this.dataName.filter(o=>o.goodsId != goods.goodsId);
     this.getAllMethod();
   },
   getAllMethod(){
     this.totalCount = 0;
     this.totalPrice = 0;
     if(this.dataName){
       for(let j of this.dataName){
        this.totalCount += j.count;
        this.totalPrice = this.totalPrice + (j.price * j.count);
       }
     }
   },
   delAllList(){
     this.totalCount = 0;
     this.totalPrice = 0;
     this.dataName = []
   },
   checkOut(){
     if(this.totalCount){
       this.totalCount = 0;
       this.totalPrice = 0;
       this.dataName = [];
       this.$message({
         message:'结账成功，感谢你又为店里出了一份力!',
         type:'success'
       })
     }else{
       this.$message.error('不能空结。老板了解你急切的心情!')
     }
   } 
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .pos-order{
    background-color: #F9FAFC;
    border-right: 1px solid #C0CCDA;
  }
  .div-btn{
    margin-top: 10px;
  }
  .title{
    height: 20px;
    border-bottom:1px solid #D3dce6;
    background-color: #F9FAFC;
    padding: 10px;
    text-align: left;
  }
  .often-list ul li{
    list-style: none;
    float: left;
    border:1px solid #F9FAFC;
    padding: 10px;
    margin: 5px;
    background-color: #fff;
    cursor: pointer;
  }
  .oprice{
    color:#58B7FF;
  }
  .goodsType{
    clear: both;
  }
  .foodCook li{
    list-style: none;
    width: 23%;
    border:1px solid #E5E9F2;
    height: auto;
    overflow: hidden;
    background-color: #fff;
    padding: 2px;
    float: left;
    margin: 2px;
    cursor: pointer;
  }
  .foodCook li span{
    display: block;
    float: left;
  }
  .foodsImg{
    width: 40%
  }
  .foodsName{
    font-size: 22px;
    padding-left: 10px;
    padding-right: 20px;
    color:brown;
  }
  .foodsPrice{
    font-size: 18px;
    padding-left: 10px;
    padding-top: 10px;
  }
  .often-list ul li {
    animation-duration: 2s;
    animation-delay: 0.2s;
    animation-iteration-count: infinite;
  }
  .title-list{
    background-color: #fff;
    padding: 10px;
    border-bottom: 1px solid #dfe6ec;
  }
</style>
