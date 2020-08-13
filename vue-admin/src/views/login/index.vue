<template>
  <div id="login_bg">
    <div class="login-wrap">
      <ul class="menu-tab">
        <li
          v-for="(item,index) in menu_list"
          :key="index"
          :class="{'active':item.current}"
          @click="handleClick(item)"
        >{{item.txt}}</li>
      </ul>

      <el-form :model="ruleForm" status-icon :rules="rules" ref="ruleForm" class="login-form">
        <el-form-item prop="username" class="item-form">
          <label>邮箱</label>
          <el-input type="text" v-model="ruleForm.username" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item prop="password" class="item-form">
          <label>密码</label>
          <el-input
            type="password"
            v-model="ruleForm.password"
            autocomplete="off"
            minlength="6"
            maxlength="20"
          ></el-input>
        </el-form-item>

        <el-form-item prop="passwords" class="item-form" v-if="model==='reg'">
          <label>重复密码</label>
          <el-input
            type="password"
            v-model="ruleForm.passwords"
            autocomplete="off"
            minlength="6"
            maxlength="20"
          ></el-input>
        </el-form-item>

        <el-form-item prop="code" class="item-form">
          <label>验证码</label>
          <el-row :gutter="10">
            <el-col :span="15">
              <el-input v-model.number="ruleForm.code" minlength="6" maxlength="6"></el-input>
            </el-col>
            <el-col :span="9">
              <el-button type="success" class="btn-form">发送验证码</el-button>
            </el-col>
          </el-row>
        </el-form-item>
        <el-form-item>
          <el-button type="danger" @click="submitForm('ruleForm')" class="btn-form login-btn">提交</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
import { reactive, ref, isRef, toRefs, onMounted } from "@vue/composition-api";
import {
  stripscript,
  validateEmail,
  validataPass,
  validataCodenumber
} from "@/utils/validate.js";
export default {
  name: "login",
  setup(props, context) {

      let validateUsername = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入用户名"));
      } else if (validateEmail(value)) {
        callback(new Error("邮箱格式不正确"));
      } else {
        callback();
      }
    };
    let validatePassword = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入密码"));
      } else if (validataPass(value)) {
        callback(new Error("请输入6至20 数字+字母组合"));
      } else {
        callback();
      }
    };

    let validatePasswords = (rule, value, callback) => {
       if(model.value==='login'){callback();}
      if (value === "") {
        callback(new Error("请再次输入密码"));
      } else if (value != ruleForm.password) {
        callback(new Error("两次密码输入错误"));
      } else {
        callback();
      }
    };

    let validateCode = (rule, value, callback) => {
      if (value === "") {
        return callback(new Error("请输入验证码"));
      } else if (validataCodenumber(value)) {
        return callback(new Error("请输入6位验证码"));
      } else {
        callback();
      }
    };
// 声明对象使用reactive
    const menu_list = reactive([
      { txt: "登录", current: true, type: "login" },
      { txt: "注册", current: false, type: "reg" }
    ]);

    
    const ruleForm = reactive({
      username: "",
      password: "",
      passwords: "",
      code: ""
    });

     const rules = reactive({
      username: [{ validator: validateUsername, trigger: "blur" }],
      password: [{ validator: validatePassword, trigger: "blur" }],
      passwords: [{ validator: validatePasswords, trigger: "blur" }],
      code: [{ validator: validateCode, trigger: "blur" }]
    });

    // 声明基础数据 使用ref
    const model = ref("login");
    const handleClick = data => {
      menu_list.forEach(element => {
        element.current = false;
      });
      data.current = true;
      model.value = data.type;
    };

    

   

    

  


    const submitForm = formName => {
     context.refs[formName].validate(valid => {
        if (valid) {
          alert("submit!");
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    };


    onMounted(()=>{
       console.log('挂载完成后') 
    })

    return{
        submitForm,
        rules,
        ruleForm,
        handleClick,
        model,
        menu_list
    }
  }
};
</script>

<style lang="scss" scoped>
#login_bg {
  height: 100vh;
  background: #344a54;
}
.login-wrap {
  width: 330px;
  margin: auto;
}

.menu-tab {
  text-align: center;
  li {
    display: inline-block;
    width: 88px;
    line-height: 36px;
    border-radius: 4px;
    color: #fff;
    cursor: pointer;
  }
  .active {
    background-color: rgba($color: #000000, $alpha: 0.1);
  }
}

.login-form {
  margin-top: 29px;
  label {
    display: block;
    font-size: 14px;
    color: #fff;
  }
  .item-form {
    margin-bottom: 13px;
  }

  .btn-form {
    width: 100%;
    display: block;
  }

  .login-btn {
    margin-top: 39px;
  }
}
</style>