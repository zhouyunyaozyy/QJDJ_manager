<template>
  <div class="cUserList">
<!--    <el-button type="primary" @click='addAdmin'>添加商家</el-button>-->
    
    <div class="searchForm">
      <p @click='showFormBool = !showFormBool'>筛选查询<i v-if='showFormBool' class="el-icon-arrow-down"></i><i v-else class="el-icon-arrow-up"></i></p>
      <el-form :inline="true" :model="formInline" class="demo-form-inline" v-if='showFormBool'>
        <el-form-item label="搜素">
          <el-input placeholder="用户名/企业名/手机号" v-model="formInline.name"></el-input>
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
          prop="id"
          label="id"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="realName"
          label="真实姓名"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="cardId"
          label="手持证件照"
          min-width="120" align='center'>
          <template slot-scope="scope">
            <img :src="scope.row.authPicturesArr[0]" style="width: 50px;height: 50px;" />
          </template>
        </el-table-column>
        <el-table-column
          prop="cardId"
          label="正面"
          min-width="120" align='center'>
          <template slot-scope="scope">
            <img :src="scope.row.authPicturesArr[1]" style="width: 50px;height: 50px;" />
          </template>
        </el-table-column>
        <el-table-column
          prop="cardId"
          label="背面"
          min-width="120" align='center'>
          <template slot-scope="scope">
            <img :src="scope.row.authPicturesArr[2]" style="width: 50px;height: 50px;" />
          </template>
        </el-table-column>
        <el-table-column
          prop="cardId"
          label="身份证号"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="createTime"
          label="提交时间"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="authFlagVal"
          label="审核状态"
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
          min-width="200" align='center'>
          <template slot-scope='scope'>
            <el-button type="primary" @click="agree(scope.row)">通过</el-button>
            <el-button type="primary" @click="refuse(scope.row)">驳回</el-button>
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
          authState: 0
        },
        form: {},
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
      onsubmit () {
        this.form = {}
        for (let val in this.formInline) {
          if (this.formInline[val] !== '') {
            this.form[val] = this.formInline[val]
          }
        }
        console.log(this.form)
        this.start = 1
        this.getTableData()
      },
      agree (row) {
        this.$axios({
          type: 'get',
          url: '/admin-mp-user/auth-success/' + row.id,
          data: {},
          fuc: (res) => {
            this.$message.success("操作成功")
            this.getTableData()
          }
        })
      },
      refuse (row) {
        this.$prompt('请输入驳回理由', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
        }).then(({ value }) => {
          this.$axios({
            type: 'put',
            url: '/admin-mp-user/auth-fail',
            data: {id: row.id, reason: value},
            fuc: (res) => {
              this.$message.success("操作成功")
              this.getTableData()
            }
          })
        }).catch(() => {
//          this.$message({
//            type: 'info',
//            message: '取消输入'
//          });       
        });
      },
      getTableData () {
        this.$axios({
          type: 'post',
          url: '/admin-mp-user/auth-list',
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