<template>
  <div class="indexSlideshowList">

    <div class="searchForm">
      <p @click='showFormBool = !showFormBool'>筛选查询<i v-if='showFormBool' class="el-icon-arrow-down"></i><i v-else class="el-icon-arrow-up"></i></p>
      <el-form :inline="true" :model="formInline" class="demo-form-inline" v-if='showFormBool'>
        <el-form-item label="输入搜索">
          <el-input v-model="formInline.clientName" placeholder="客户名"></el-input>
        </el-form-item>
        <el-form-item label="派发状态">
          <el-select v-model='formInline.dispatch' placeholder='请选择对应状态' clearable>
            <el-option v-for='item in categoryDispatchList' :label='item.label' :key='item.value' :value='item.value'></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="状态">
          <el-select v-model='formInline.state' placeholder='请选择对应状态' clearable>
            <el-option v-for='item in categoryList' :label='item.label' :key='item.value' :value='item.value'></el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="onsubmit">查询</el-button>
        </el-form-item>
      </el-form>
    </div>

    <el-table
    :data="tableData"
    style="width: 100%" border>
      <el-table-column
        prop="orderNumber"
        label="订单编号"
        min-width="120" align='center'>
      </el-table-column>
      <el-table-column
        prop="companyName"
        label="网店名"
        min-width="120" align='center'>
      </el-table-column>
      <el-table-column
        prop="clientName"
        label="客户名"
        min-width="120" align='center'>
      </el-table-column>
      <el-table-column
        prop="clientPhone"
        label="联系方式"
        min-width="120" align='center'>
      </el-table-column>
      <el-table-column
        prop="address"
        label="地址"
        min-width="120" align='center'>
      </el-table-column>
      <el-table-column
        prop="productSize"
        label="产品平方数"
        min-width="120" align='center'>
      </el-table-column>
      <el-table-column
        prop="totalPrice"
        label="佣金"
        min-width="120" align='center'>
      </el-table-column>
      <el-table-column
        prop="remark"
        label="备注"
        min-width="120" align='center'>
      </el-table-column>
      <el-table-column
        prop="stateFlagVal"
        label="订单状态"
        min-width="120" align='center'>
      </el-table-column>
      <el-table-column
        prop="stateVal"
        label="操作"
        min-width="300" align='center'>
        <template slot-scope="scope">
          <el-button @click="paidan(scope.row)" v-if="scope.row.dispatchFlag != 2">派单</el-button>
          <el-button>关闭</el-button>
          <el-button @click='goDetail(scope.row)'>查看</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
    layout="prev, pager, next"
    :total="total" :page-size="20" @current-change="handleCurrentChange"
      :current-page.sync="start">
  </el-pagination>
  <el-dialog title="师傅列表" :visible.sync="dialogTableVisible">
    <el-table :data="gridData"
              highlight-current-row
              @current-change="handleTableChange" @close="tableClose">
      <el-table-column property="id" label="id" width="150"></el-table-column>
      <el-table-column property="realName" label="姓名" width="200"></el-table-column>
      <el-table-column property="phone" label="电话" width="200"></el-table-column>
    </el-table>
    <el-switch
      style="display: block"
      v-model="value4"
      active-color="#13ce66"
      inactive-color="#ff4949"
      active-text="需要佣金"
      inactive-text="不需要佣金">
    </el-switch>
    <span slot="footer" class="dialog-footer">
      <el-button type="primary" @click="paidanSure">确 定</el-button>
    </span>
  </el-dialog>
  </div>
</template>

<script>
  export default {
    name: 'unlineBusinessList',
    data () {
      return {
        dialogTableVisible: false,
        gridData: [],
        currentRow: null,
        id: null,
        value4: true,

        form: {},
        tableData: [],
        formInline: {
          clientName: '',
          dispatch: '-1',
          state: '-1'
        },
        form: {},
//        -1-全部 1-未接单，2-未预约，3-已预约，4-已开工，5-已完工，6-未审核，7-已审核，8-已失效，9-已撤回
        categoryList: [{
          value: '-1',
          label: "全部"
        },{
          value: 1,
          label: "未接单"
        },{
          value: 2,
          label: "未预约"
        },{
          value: 3,
          label: "已预约"
        },{
          value: 4,
          label: "已开工"
        },{
          value: 5,
          label: "已完工"
        },{
          value: 6,
          label: "未审核退单"
        },{
          value: 7,
          label: "已审核退单"
        },{
          value: 8,
          label: "已失效"
        },{
          value: 9,
          label: "已撤回订单"
        }],
//        -1-全部 1-未接单，2-未预约，3-已预约，4-已开工，5-已完工，6-未审核，7-已审核，8-已失效，9-已撤回
        categoryDispatchList: [{
          value: '-1',
          label: "全部"
        },{
          value: '1',
          label: "未派发"
        },{
          value: '2',
          label: "已派发"
        }],
        start: 1,
        total: 0,
        showFormBool: true, // 是否显示过滤框
      }
    },
    created () {
      this.onsubmit()
    },
    mounted () {},
    methods: {
      handleTableChange (row) {
        this.currentRow = row
      },
      paidanSure () {
        if (this.currentRow) {
          this.$axios({
            type: 'put',
            url: '/admin-order/dispatch/' + this.id + '/' + this.currentRow.id + '/' + (this.value4 ? 1 : 0),
            data: {},
            fuc: (res) => {
              this.$message.success('派单成功')
              this.dialogTableVisible = false
            }
          })
        }

      },
      tableClose () {
        this.currentRow = null
        this.id = null
        this.value4 = true
      },
      paidan (row) {
        this.$axios({
          type: 'get',
          url: '/admin-order/users/' + row.areaCode + '/' + row.productId,
          data: {},
          fuc: (res) => {
            this.gridData = res
            this.id = row.id
            this.dialogTableVisible = true
          }
        })
      },
      goDetail (row) {
        this.$router.push({path: '/orderAll/orderAllDetail', query: {id: row.id}})
      },
      onsubmit () {
        this.form = {}
        for (let val in this.formInline) {
          if (this.formInline[val] !== '') {
            this.form[val] = this.formInline[val]
          }
        }
        console.log(this.form)
        this.getTableData()
      },
      handleCurrentChange (val) {
        this.start = val
        this.getTableData()
      },
      getTableData () {
        this.$axios({
          type: 'post',
          url: '/admin-order/list',
          data: {current: this.start, size: 20, ...this.form},
          fuc: (res) => {
            this.tableData = res.records
            this.total = res.total
          }
        })
      }
    }
  }
</script>
<style scoped="true">
  .indexSlideshowList{
    margin: 10px 20px 0;
    overflow: hidden;
  }
</style>
