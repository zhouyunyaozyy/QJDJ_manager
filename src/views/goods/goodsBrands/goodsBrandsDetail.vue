<template>
  <div class="goodsBrandsDetail">
    <el-form ref="form" :model="form" label-width="120px" :rules='rules'>
      <el-form-item label="商品名称" prop='name'>
        <el-input v-model="form.name" placeholder='请填写商品名称'></el-input>
      </el-form-item>
      <el-form-item label="最低价" prop='minCost'>
        <el-input v-model="form.minCost" placeholder='例：15'></el-input>
      </el-form-item>
      <el-form-item label="最高价" prop='maxCost'>
        <el-input v-model="form.maxCost" placeholder='例：25'></el-input>
      </el-form-item>
      <el-form-item label="价格单位" prop='unit'>
        <el-input v-model="form.unit" placeholder='例：元/每平方'></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">提交</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
  export default {
    data() {
      return {
        form: {
          name: '',
          minCost: '',
          maxCost: '',
          unit: '元/每平方'
        },
        rules: {
          name: [{ required: true, message: '请填写商品名称', trigger: 'blur' }],
          maxCost: [{ required: true, message: '请填写价格', trigger: 'blur' }],
          minCost: [{ required: true, message: '请填写价格', trigger: 'blur' }],
          unit: [{ required: true, message: '请填写价格单位', trigger: 'blur' }]
        }
      }
    },
    created() {
      if (this.$route.query.id) {
        this.$axios({
          type: 'get',
          url: '/admin-product/' + this.$route.query.id,
          data: {},
          fuc: (res) => {
            this.form = res
          }
        })
      }
    },
    methods: {
      onSubmit() {
        this.$refs['form'].validate((valid) => {
          if (valid) {
            const _form = JSON.parse(JSON.stringify(this.form))
            this.$axios({
              type: _form.id ? 'put' : 'post',
              url: '/admin-product',
              data: _form,
              fuc: (res) => {
                this.$deleteOneTag('/goodsBrands/goodsBrandsList')
              }
            })
          }
        })
      }
    }
  }
</script>
<style scoped="true">
  .goodsBrandsDetail{
    margin: 10px 20px 0;
    overflow: hidden;
  }
  .goodsBrandsDetail>.el-form{
    width: 400px;
    margin: 20px;
  }
</style>
<style>
  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }
  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }
  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }
</style>
