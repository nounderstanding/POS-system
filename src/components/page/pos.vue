<template>
  <div class="pos">
    <el-row>
      <el-col :span='7' class="pos-order" id="order-list">
        <el-tabs>
          <el-tab-pane label="点餐">
            <el-table :data="tableData" border style="width:100%;">
              <el-table-column prop="goodsName" label="商品名称"></el-table-column>
              <el-table-column prop="count" label="数量" width="90"></el-table-column>
              <el-table-column prop="price" label="金额" width="90"></el-table-column>
              <el-table-column label="操作" width="100" fixed="right">
                <template scope="scope">
                  <el-button type="text" size="small" @click="delSigleGoods(scope.row)">删除</el-button>
                  <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                </template>
              </el-table-column>
            </el-table>
            <div class="div-total">
              <small>数量：</small><span class="red">{{totalCount}}</span>
              <small>金额：</small><span class="red">￥{{totalMoney}}</span>
            </div>
            <div class="div-btn">
              <el-button type="warning">挂单</el-button>
              <el-button type="danger" @click="delAllGoods()">删除</el-button>
              <el-button type="success" @click="checkout()">结账</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label="挂单">
            挂单
          </el-tab-pane>
          <el-tab-pane label="外卖">
            外卖
          </el-tab-pane>
        </el-tabs>

      </el-col>
      <el-col :span='17'>
        <div class="often-goods">
          <div class="title">常用商品</div>
          <div class="often-goods-list">
            <ul>
              <li v-for="goods in oftenGoods" @click="addOrderList(goods)">
                <span>{{goods.goodsName}}</span>
                <span class="o-price">￥{{goods.price}}</span>
              </li>
            </ul>
          </div>
        </div>

        <div class="goods-type">
          <el-tabs>
            <el-tab-pane label="汉堡">
              <div>
                <ul class="cookList">
                  <li v-for="goods in type0Goods" @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                    <div class="foodStyle">
                      <div class="foodName">{{goods.goodsName}}</div>
                      <div class="foodPrice">￥{{goods.price}}</div>
                    </div>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="小食">
              <div>
                <ul class="cookList">
                  <li v-for="goods in type1Goods" @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                    <div class="foodStyle">
                      <div class="foodName">{{goods.goodsName}}</div>
                      <div class="foodPrice">￥{{goods.price}}</div>
                    </div>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="饮料">
              <div>
                <ul class="cookList">
                  <li v-for="goods in type2Goods" @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                    <div class="foodStyle">
                      <div class="foodName">{{goods.goodsName}}</div>
                      <div class="foodPrice">￥{{goods.price}}</div>
                    </div>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="套餐">
              <div>
                <ul class="cookList">
                  <li v-for="goods in type3Goods" @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                    <div class="foodStyle">
                      <div class="foodName">{{goods.goodsName}}</div>
                      <div class="foodPrice">￥{{goods.price}}</div>
                    </div>
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
  name:'pos',
  data(){
    return {
      tableData:[],
      oftenGoods:[],
      type0Goods:[],
      type1Goods:[],
      type2Goods:[],
      type3Goods:[],
      totalCount:0,
      totalMoney:0,
    }
  },
  created:function(){
    axios.get("http://jspang.com/DemoApi/oftenGoods.php")
    .then(response=>{
      console.log(response);
      this.oftenGoods=response.data;
    })
    .catch(error=>{
      alert('网络错误，不能访问')
    })

    axios.get("http://jspang.com/DemoApi/typeGoods.php")
    .then(response=>{
      this.type0Goods=response.data[0];
      this.type1Goods=response.data[1];
      this.type2Goods=response.data[2];
      this.type3Goods=response.data[3];
    })
    .catch(error=>{
      alert('网络错误，不能访问')
    })


  },
  mounted:function(){
    var orderHeight=document.body.clientHeight;
    console.log(orderHeight);
    document.getElementById("order-list").style.height=orderHeight+"px";
  },
  methods:{
    addOrderList(goods){
      //商品是否存在于订单列表中
      let isHave=false;
      for(let i=0;i<this.tableData.length;i++){
        if(this.tableData[i].goodsId==goods.goodsId){
          isHave=true;
        }
      }
      //根据判断的值编写业务逻辑
      if(isHave){
        // 改变列表中商品数量
        let arr=this.tableData.filter(o=>o.goodsId==goods.goodsId);
        arr[0].count++;
      }else{
        let newGoods={goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1};
        this.tableData.push(newGoods);
      }
      this.getAllMoney();
    },
    delSigleGoods:function(goods){
        this.tableData=this.tableData.filter(o=>o.goodsId!=goods.goodsId);
        this.getAllMoney();
    },
    delAllGoods:function(){
      this.tableData=[];
      this.totalCount=0;
      this.totalMoney=0;
    },
    // 汇总数量和金额
    getAllMoney:function(){
       this.totalCount=0;
       this.totalMoney=0;
       if(this.tableData){
          // this.tableData.forEach(function(element) {
          //   this.totalCount+=element.count;
          //   this.totalMoney+=element.count*element.price;
          // }, this);
           this.tableData.forEach((element)=>{
            this.totalCount+=element.count;
            this.totalMoney+=element.count*element.price;
          });
       }
    },
    //模拟结账
    checkout(){
      if(this.totalCount!=0){
        this.delAllGoods();
        this.$message({
          message:"结账成功，欢迎下次光临",
          type:"success"
        })
      }else{
        this.$message.error("不能空结，老板了解你急切的心情！")
      }
    }
    
  }
}
</script>

<style>
  .pos-order{
    background-color: #f9fafc;
    border-right:1px solid #c0ccda;
  }
  .div-btn{margin-top: 10px;}
  .el-tabs__item{width: 100%;}
  .title{
    height: 39px;
    line-height: 39px;
    border-bottom: 1px solid #d3dce6;
    background-color: #f9fafc;
    font-size: 18px;
  }
  .often-goods-list ul li{
    list-style: none;
    float: left;border:1px solid #e5e9f2;
    margin:10px;
    padding:10px;
    background: #fff;
    cursor: pointer;
  }
  .o-price{
    color:#58b7ff;

  }
  .goods-type{
    clear: both;
  }
  .cookList li{
    list-style: none;
    width: 25%;
    border:1px solid #e5e9f2;
    height: auto;
    overflow: hidden;
    background-color: #fff;
    padding: 2px;
    float: left;
    margin: 2px;
    cursor: pointer;
  }
  .cookList li span{
    display: block;
    float: left;
  }
  .foodStyle{
    float: left;
    }
  .foodImg{
    width: 40%;
  }
  .foodName{
    font-size: 16px;
    padding-left: 10px;
    color:brown;
  }
  .foodPrice{
    font-size: 16px;
    padding-left: 10px;
    padding-top: 10px;
    text-align: left;
  }
  .div-total{
    height: 48px;
    line-height: 48px;
    background-color: #fff;
    border-bottom:1px solid #ebeef5;
    text-align: left;
    padding-left: 10px;
    color: #909399; 
    font-size: 18px;
  }
  .red{
    color: red;
    margin-right: 10px;
  }
</style>

