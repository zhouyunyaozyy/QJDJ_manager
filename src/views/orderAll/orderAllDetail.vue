<template>
  <div class="orderAllDetail">
    <div class="ordedrDetail">
      <span>订单信息</span>
      <p>订单编号：{{form.orderNumber}}</p>
      <p>网店名：{{form.companyName}}</p>
      <p>姓名：{{form.clientName}}</p>
      <p>联系方式：{{form.clientPhone}}</p>
      <p>地址：{{form.address}}</p>
      <p>备注：{{form.remark}}</p>
    </div>
    <div class="userDetail">
      <span>商品信息</span>
      <p>安装费用：{{form.productCost}}</p>
      <p>佣金费用：{{form.totalPrice}}</p>
      <p>平方量：{{form.productSize}}</p>
      <p>详细信息：{{form.productInfo}}</p>
    </div>
    <el-table
    :data="tableData"
    style="width: 100%">
      <el-table-column
        label="用户列表" width='100%'>
        <el-table-column
          prop="phone"
          label="手机号"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="account"
          label="用户名"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="companyName"
          label="企业名称"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="createTime"
          label="注册时间"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="orderTotal"
          label="上传订单总数"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="state"
          label="商家状态"
          min-width="120" align='center'>
        </el-table-column>
<!--
        <el-table-column
          prop="recommend_num"
          label="用户推荐人数"
          min-width="120" align='center'>
        </el-table-column>
-->
        <el-table-column
          prop="id"
          label="操作"
          min-width="120" align='center'>
          <template slot-scope='scope'>
            <el-dropdown @command="handleCommand($event, scope.row)" trigger="click">
              <el-button type="primary">
                操作<i class="el-icon-arrow-down el-icon--right"></i>
              </el-button>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item command="a" v-if="scope.row.stateFlag == 1">冻结</el-dropdown-item>
                <el-dropdown-item command="b" v-if="scope.row.stateFlag == 2">激活</el-dropdown-item>
                <el-dropdown-item command="d">编辑</el-dropdown-item>
                <el-dropdown-item command="c">重置密码</el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </template>
        </el-table-column>
      </el-table-column>
    </el-table>
  </div>
</template>
<script>
  export default {
    data () {
      return {
        tableData: [],
        form: {}
      }
    },
     created () {
      this.$axios({
        type: 'get',
        url: '/admin-order/' + this.$route.query.id,
        data: {},
        fuc: (res) => {
          this.form = res
        }
      })
     },
  }
</script>
<style scoped="true">
  .orderAllDetail {
    margin: 10px 20px 0;
    overflow: hidden;
  }
  .ordedrDetail, .userDetail{
    overflow: hidden;
/*    margin: 10px;*/
    float: left;
/*    box-sizing: border-box;*/
    width: 50%;
/*    display: inline-block;*/
  }
  .ordedrDetail>span, .userDetail>span{
    font-size: 18px;
  }
</style>