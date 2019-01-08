<template>
  <div class="goodsBrandsList">
    <el-button type='primary' @click='addBrand'>添加商品分类</el-button>

    <el-table
    :data="tableData"
    style="width: 100%">
      <el-table-column
        label="商品列表">
        <el-table-column
          prop="id"
          label="ID"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="name"
          label="商品名称"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="priceRange"
          label="价格"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="unit"
          label="价格单位"
          min-width="120" align='center'>
        </el-table-column>
        <el-table-column
          prop="id"
          label="操作"
          min-width="240" align='center'>
          <template slot-scope='scope'>
            <el-button @click='editBrand(scope.row)'>编辑</el-button>
            <el-button @click='removeBrand(scope.row)'>删除</el-button>
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
    name: 'goodsBrandsList',
    data() {
      return {
        tableData: [],
        start: 1,
        total: 0
      }
    },
    created() {
      this.getTableData()
    },
    mounted() {},
    methods: {
      getTableData() {
        this.$axios({
          type: 'post',
          url: "/admin-product/" + this.start + "/" + 20,
          data: {},
          fuc: (res) => {
            this.tableData = res.records
            this.total = res.total
          }
        })
      },
      onSubmit() {
        this.start = 1
        this.getTableData()
      },
      handleCurrentChange(val) {
        this.start = val
        this.getTableData()
      },
      editBrand(row) {
        this.$router.push({ path: '/goodsBrands/goodsBrandsDetail', query: { bid: row.bid }})
      },
      removeBrand(row) {
        this.$axios({
          type: 'delete',
          url: '/admin-product/' + row.id,
          data: {},
          fuc: (res) => {
            this.$message.success('操作成功')
            this.getTableData()
          }
        })
      },
      addBrand() {
        this.$router.push({ path: '/goodsBrands/goodsBrandsDetail' })
      }
    }
  }
</script>
<style scoped="true">
  .goodsBrandsList{
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
