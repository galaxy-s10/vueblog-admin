<template>
  <div class="app-container" v-if="user">
    <el-form ref="user" :model="user" :rules="rules" label-width="80px">
      <el-form-item label="id" hidden>
        <el-input v-model="user.id" />
      </el-form-item>
      <el-form-item label="Name" prop="username">
        <el-input v-model="user.username" />
      </el-form-item>
      <el-form-item label="Avatar">
        <upload-com ref="uploadCom" :imgList="imgList"></upload-com>
      </el-form-item>
      <el-form-item label="Title" prop="title">
        <el-input v-model="user.title" />
      </el-form-item>
      <el-form-item label="Role">
        <el-radio v-model="user.role" label="user">用户</el-radio>
        <el-radio v-model="user.role" label="admin">管理员</el-radio>
      </el-form-item>
      <el-form-item label="Password" prop="password">
        <el-input type="password" show-password v-model="user.password" />
      </el-form-item>
      <el-form-item>
        <el-button type="success" @click="editUser()">点击修改</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import { find, edit } from "@/api/user";
import uploadCom from "@/components/upload/index";
export default {
  components: { uploadCom },
  data() {
    return {
      imgList: [],
      user: null,
      newPassword: "",
      rules: {
        username: [
          { required: true, message: "请输入新UserName", trigger: "blur" },
          {
            min: 3,
            max: 10,
            message: "UserName长度要求在 3 到 10 个字符！",
            trigger: "blur"
          }
        ],
        title: [
          {
            min: 3,
            max: 20,
            message: "Title长度要求在 3 到 20 个字符或者不输入！",
            trigger: "blur"
          }
        ],
        password: [
          { required: true, message: "请输入新Password", trigger: "blur" },
          {
            min: 6,
            max: 12,
            message: "Password长度要求在 6 到 12 个字符！",
            trigger: "blur"
          }
        ]
      }
    };
  },
  created() {
    find(this.$route.params.id).then(res => {
      res.password = "";
      this.user = res;
      if (this.user.avatar) {
        this.imgList.push({ name: "", url: this.user.avatar });
      }
    });
  },
  methods: {
    editUser() {
      this.$refs.user.validate(res => {
        if (res) {
          this.$confirm("确定修改该用户？", {
            confirmButtonText: "确定",
            cancelButtonText: "取消",
            type: "warning"
          }).then(() => {
            if (
              this.user.avatar != null &&
              this.$refs.uploadCom.imgList.length != 0
            ) {
              this.user.avatar = this.$refs.uploadCom.imgList[0].url;
            }
            if (
              this.user.avatar != null &&
              this.$refs.uploadCom.imgList.length == 0
            ) {
              this.user.avatar = null;
            }
            if (
              this.user.avatar == null &&
              this.$refs.uploadCom.imgList.length != 0
            ) {
              this.user.avatar = this.$refs.uploadCom.imgList[0].url;
            }
            edit(this.user)
              .then(res => {
                this.$message({
                  type: "success",
                  message: "修改用户成功!"
                });
                this.$router.push({ name: "User" });
              })
              .catch(err => {
                this.$notify.error({
                  title: "错误",
                  message: "修改用户失败！"
                });
              });
          });
        }
      });
    }
  }
};
</script>

<style scoped>
</style>
