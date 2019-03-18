<template>
  <div class="cUserList">
    <el-button type="primary" @click='addAdmin'>添加商家</el-button>
    
    <div class="searchForm">
      <p @click='showFormBool = !showFormBool'>筛选查询<i v-if='showFormBool' class="el-icon-arrow-down"></i><i v-else class="el-icon-arrow-up"></i></p>
      <el-form :inline="true" :model="formInline" class="demo-form-inline" v-if='showFormBool'>
        <el-form-item label="搜素">
          <el-input placeholder="用户名/企业名/手机号" v-model="formInline.name"></el-input>
        </el-form-item>
        <el-form-item label="注册时间">
          <el-date-picker
            v-model="formInline.regDate"
            type="daterange"
            range-separator="至"
            format="yyyy 年 MM 月 dd 日"
            value-format="yyyy-MM-dd"
            start-placeholder="开始日期"
            end-placeholder="结束日期">
          </el-date-picker>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="onsubmit">查询</el-button>
        </el-form-item>
      </el-form>
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
    <el-pagination
    layout="prev, pager, next"
    :total="total" :page-size="20" @current-change="handleCurrentChange"
      :current-page.sync="start">
  </el-pagination>

  </div>
</template>

<script>
  export default {
    name: 'cUserList',
    data () {
      return {
        tableData: [],
        formInline: {
          name: '',
          regDate: []
        },
        form: {},
        start: 1,
        total: 0,
        showFormBool: true, // 是否显示过滤框
      }
    },
    created () {
      this.getTableData()
    },
    mounted () {},
    methods: {
      onsubmit () {
        this.form = {}
        for (let val in this.formInline) {
          if (this.formInline[val] !== '') {
            if (val === "regDate") {
              this.form[val] = this.formInline[val].join(',')
            } else {
              this.form[val] = this.formInline[val]
            }
          }
        }
        console.log(this.form)
        this.start = 1
        this.getTableData()
      },
      handleCommand (command, row) {
        console.log(command, row)
        if (command === 'a') {
//          冻结
          this.$axios({
            type: 'get',
            url: '/admin/shop/disable/' + row.id,
            data: {},
            fuc: (res) => {
              this.$message.success("操作成功")
              this.getTableData()
            }
          })
        } else if (command === 'b') {
//          激活
          this.$axios({
            type: 'get',
            url: '/admin/shop/enabled/' + row.id,
            data: {},
            fuc: (res) => {
              this.$message.success("操作成功")
              this.getTableData()
            }
          })
          
        } else if (command === 'c') {
//          重置密码
          this.$axios({
            type: 'get',
            url: '/admin/shop/reset-password/' + row.id,
            data: {},
            fuc: (res) => {
              this.$message.success("操作成功")
//                this.getTableData()
            }
          })
        } else if (command === 'd') {
//          编辑
          this.$router.push({path: '/cUser/cUserDetail', query: {id: row.id}})
        }
      },
      getTableData () {
        this.$axios({
          type: 'post',
          url: '/admin/shop/list',
          data: {current: this.start, size: 20, ...this.form},
          fuc: (res) => {
            this.tableData = res.records
            this.total = res.total
          }
        })
      },
      handleCurrentChange (val) {
        this.start = val
        this.getTableData()
      },
      detailUser (row) {
        this.$router.push({path: '/cUser/cUserDetail', query: {id: row.user_id}})
      },
      addAdmin () {
        this.$router.push({path: '/cUser/cUserDetail'})
      }
    }
  }
</script>
<style scoped="true">
  .cUserList{
    margin: 10px 20px 0;
    overflow: hidden;
  }
  .searchForm{
    margin: 10px 0;
  }
  .searchForm>p{
    width: 100%;
    background-color: #f5f7fa;
    padding: 10px;
    color: #909399;
    font-size: 14px;
    font-weight: bold;
    cursor: pointer;
  }
  .searchForm>p>i{
    margin-left: 20px;
  }
</style>