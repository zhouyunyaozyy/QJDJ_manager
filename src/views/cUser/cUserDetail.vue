<template>
  <div class="adminDetail">
    <el-form ref="form" :model="form" label-width="80px" :rules='rules'>
      <el-form-item label="用户名" prop='account'>
        <el-input v-model="form.account"></el-input>
      </el-form-item>
      <el-form-item label="企业名称" prop='companyName'>
        <el-input v-model="form.companyName"></el-input>
      </el-form-item>
      <el-form-item label="手机号" prop='phone'>
        <el-input v-model="form.phone"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">提交</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
  export default {
    data () {
      return {
        form: {
          account: '',
          companyName: '',
          phone: ''
        },
        rules: {
//          account: [{ required: true, message: '请填写用户名', trigger: 'blur' },{validator: this.checkUsername, trigger: 'blur'}],
          account: [{ required: true, message: '请填写用户名', trigger: 'blur' }],
          companyName: [{ required: true, message: '请填写用户名', trigger: 'blur' }],
          phone: [{ required: true, message: '请填写密码', trigger: 'blur' }],
        }
      }
    },
    created () {
      if (this.$route.query.id) {
        this.$axios({
          type: 'get',
          url: '/admin/shop/' + this.$route.query.id,
          data: {},
          fuc: (res) => {
            this.form.account = res.account
            this.form.companyName = res.companyName
            this.form.phone = res.phone
            this.form.id = this.$route.query.id
          }
        })
      }
    },
    methods: {
      checkUsername (rule, value, callback) {
        if (this.$route.query.id) {
          callback()
        } else {
          this.$axios({
            type: 'post',
            url: '/Auth/checkuserexist',
            data: {username: this.form.username},
            fuc: (res) => {
              console.log(5, res)
              if (res.code == 200) {
                callback()
              } else {
                callback(new Error(res.info))
              }
            }
          })
        }
      },
      onSubmit () {
        this.$refs['form'].validate((valid) => {
          if (valid) {
            this.$axios({
              type: this.form.id ? 'put' : "post",
              url: '/admin/shop' + (this.form.id ? "/" + this.form.id : ''),
              data: this.form,
              fuc: (res) => {
                this.$message.success('操作成功')
                this.$deleteOneTag('/cUser/cUserList')
              }
            })
          }
        })
      }
    }
  }
</script>
<style scoped="true">
  .adminDetail{
    margin: 10px 20px 0;
    overflow: hidden;
  }
  .adminDetail>.el-form{
    width: 400px;
    margin: 20px;
  }
</style>