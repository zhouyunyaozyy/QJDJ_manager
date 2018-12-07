<template>
  <div class="cUserList">
    <el-button type="primary" @click='addAdmin'>添加商家</el-button>
    
    <div class="searchForm">
      <p @click='showFormBool = !showFormBool'>筛选查询<i v-if='showFormBool' class="el-icon-arrow-down"></i><i v-else class="el-icon-arrow-up"></i></p>
      <el-form :inline="true" :model="formInline" class="demo-form-inline" v-if='showFormBool'>
        <el-form-item label="搜素">
          <el-input placeholder="用户名/企业名/手机号" v-model="formInline.key_a"></el-input>
        </el-form-item>
        <el-form-item label="注册时间">
          <el-date-picker
            v-model="formInline.key_time"
            type="daterange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期">
          </el-date-picker>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="onSubmit">查询</el-button>
        </el-form-item>
      </el-form>
    </div>

    <el-table
    :data="tableData"
    style="width: 100%">
      <el-table-column
        label="用户列表" width='100%'>
        <el-table-column
          prop="user_phone"
          label="手机号"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="nickname"
          label="用户名"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="reg_source"
          label="企业名称"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="create_at"
          label="注册时间"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="recommend_code"
          label="上传订单总数"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="recommend_code"
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
            <el-dropdown @command="handleCommand" trigger="click">
              <el-button type="primary">
                更多菜单<i class="el-icon-arrow-down el-icon--right"></i>
              </el-button>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item command="a">冻结</el-dropdown-item>
                <el-dropdown-item command="b">激活</el-dropdown-item>
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
          key_a: '',
          key_time: []
        },
        start: 1,
        total: 0,
        showFormBool: true, // 是否显示过滤框
      }
    },
    created () {
//      this.getTableData()
    },
    mounted () {},
    methods: {
      handleCommand (command) {
        console.log(command)
      },
      getTableData () {
        this.$axios({
          type: 'post',
          url: '/User/userlist',
          data: {page: this.start, ...this.formInline},
          fuc: (res) => {
            this.tableData = res.data.userlist
            this.total = res.data.allnum
          }
        })
      },
      handleCurrentChange (val) {
        this.start = val 
        this.getTableData()
      },
      onSubmit () {
        this.start = 1
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