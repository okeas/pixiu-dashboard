<template>
  <div class="login-page">
    <div class="login-container">
      <div class="tab-container">
        <div class="tab-item">
          <div selected="selected" class="u-tabs_item_3_DeFFee">
            {{ $t(`login.user_login`) }}
          </div>
          <div class="change-language-container">
            <div style="cursor: pointer" @click="change">
              <el-icon class="el-input__icon">
                <component is="Switch" />
              </el-icon>
              {{ $t(`login.switch_language`) }}
            </div>
          </div>
        </div>
        <el-form
          :model="data.loginInfo"
          :rules="data.rules"
          ref="loginFormRef"
          style="width: 100%; height: 30%"
        >
          <el-form-item prop="name">
            <el-input
              v-model="data.loginInfo.name"
              :placeholder="$t(`login.username`)"
              clearable
              maxlength="128"
              size="large"
              @keyup.enter.native="login"
            >
              <template #prefix>
                <el-icon class="el-input__icon">
                  <component is="UserFilled" />
                </el-icon>
              </template>
            </el-input>
          </el-form-item>
          <el-form-item prop="password">
            <el-input
              v-model="data.loginInfo.password"
              :placeholder="$t(`login.password`)"
              show-password
              clearable
              maxlength="128"
              size="large"
              @keyup.enter.native="login"
            >
              <template #prefix>
                <el-icon class="el-input__icon">
                  <component is="Lock"></component>
                </el-icon>
              </template>
            </el-input>
          </el-form-item>
        </el-form>
        <div class="button-group">
          <div class="forget-container">
            <el-button
              class="forget-button"
              type="text"
              @click="forget"
              size="large"
            >
              {{ $t(`login.forget`) }}
            </el-button>
          </div>
          <el-button
            class="login-button"
            size="large"
            @click="login"
            :loading="data.load"
          >
            {{ $t(`login.login`) }}</el-button
          >
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { reactive, getCurrentInstance, ref } from "vue";
const { proxy } = getCurrentInstance();
const data = reactive({
  loginInfo: {
    name: "",
    password: "",
  },
  load: false,
  // 图标高级
  Lock: "",
  UserFilled: "",
  rules: {
    name: [{ required: true, message: "请输入用户名", trigger: "blur" }],
    password: [{ required: true, message: "请输入密码", trigger: "blur" }],
  },
});
const forget = () => {
  proxy.$message.error("忘记密码，请联系管理员");
};

const change = () => {
  if (proxy.$i18n.locale === "zh") {
    proxy.$i18n.locale = "en";
  } else {
    proxy.$i18n.locale = "zh";
  }
};
const loginFormRef = ref(null);
const login = () => {
  loginFormRef.value.validate(async (valid) => {
    if (valid) {
      data.load = true;

      // 发送登陆请求
      let res = await proxy.$http({
        method: "post",
        url: "/users/login",
        data: data.loginInfo,
      });
      data.load = false;
      if (res.code != 200) {
        proxy.$message.error(res.message);
        return;
      }
      const token = res.result;
      localStorage.setItem("token", token);
      localStorage.setItem("account", data.loginInfo.name);
      proxy.$message.success("登陆成功");
      proxy.$router.push("/index");
    }
  });
};
</script>

<style scoped>
/* 有用 */
.login-page {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
}
.login-container {
  width: 450px;
  height: 380px;
  box-sizing: border-box;
  background: #fff;
  transition: box-shadow 0.2s;
  box-shadow: 0 0 10px 0 rgb(80 90 109 / 16%);
  display: flex;
  align-items: center;
  justify-content: center;
}
.tab-container {
  height: 80%;
  width: 80%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: flex-start;
}
.tab-item {
  width: 100%;
  display: flex;
}

.u-tabs_item_3_DeFFee {
  cursor: pointer;
  display: inline-block;
  box-sizing: content-box;
  height: 30px;
  line-height: 30px;
  padding: 0 20px;
  border: 1px solid #ece5e3;
  border-bottom: none;
  /* margin-bottom: -1px; */
}

.change-language-container {
  width: 70%;
  border-bottom: 1px solid #ece5e3;
  display: flex;
  justify-content: flex-end;
  align-items: center;
  color: #508ae2;
}

/* 有用 */
.u-tabs_item_3_DeFFee[selected] {
  background: #fff;
  color: #508ae2;
  border-color: #e0e6ed;
  border-top: 2px solid #508ae2;
}
.u-tabs_head_3_DeFFee {
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  height: 30px;
  border-bottom: 1px solid #ece5e3;
  width: 100%;
}
.button-group {
  width: 100%;
  height: 40%;
}
.forget-container {
  display: flex;
  width: 100%;
  justify-content: flex-end;
}
.forget-button {
  font-size: inherit;
  color: #6383dc;
}
.login-button {
  width: 100%;
  background: #508ae2;
  color: #fff;
  font-size: 17px;
}
.el-form-item {
  width: 100%;
}
</style>
