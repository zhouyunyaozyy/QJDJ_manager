<template>
  <div class="adminDetail">
    <el-form ref="form" :model="form" label-width="80px" :rules='rules'>
      <el-form-item label="用户名" prop='account'>
        <el-input v-model="form.account"></el-input>
      </el-form-item>
      <el-form-item label="姓名" prop='realName'>
        <el-input v-model="form.realName"></el-input>
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
    data() {
      return {
        form: {
          account: '',
          realName: '',
          phone: ''
        },
        rules: {
        //          account: [{ required: true, message: '请填写用户名', trigger: 'blur' },{validator: this.checkUsername, trigger: 'blur'}],
          account: [{ required: true, message: '请填写用户名', trigger: 'blur' }],
          realName: [{ required: true, message: '请填写姓名', trigger: 'blur' }],
          phone: [{ required: true, message: '请填写手机号', trigger: 'blur' }]
        }
      }
    },
    created() {
      if (this.$route.query.id) {
        this.$axios({
          type: 'get',
          url: '/admin-user/' + this.$route.query.id,
          data: {},
          fuc: (res) => {
            this.form.account = res.account
            this.form.realName = res.realName
            this.form.phone = res.phone
            this.form.id = this.$route.query.id
          }
        })
      }
    },
    methods: {
      checkUsername(rule, value, callback) {
        if (this.$route.query.id) {
          callback()
        } else {
          this.$axios({
            type: 'post',
            url: '/Auth/checkuserexist',
            data: { username: this.form.username },
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
      onSubmit() {
        this.$refs['form'].validate((valid) => {
          if (valid) {
            this.$axios({
              type: this.form.id ? 'put' : 'post',
              url: '/admin-user' + (this.form.id ? '/' + this.form.id : ''),
              data: this.form,
              fuc: (res) => {
                this.$message.success('操作成功')
                this.$deleteOneTag('/user/userList')
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
