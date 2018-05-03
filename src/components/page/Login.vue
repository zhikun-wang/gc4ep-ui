<template>
    <div class="login-wrap">
        <div class="ms-title">后台管理系统</div>


        <div class="ms-login">
            <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="0px" class="demo-ruleForm">
                <el-form-item prop="username">
                    <el-input v-model="ruleForm.username" placeholder="username">admin</el-input>
                </el-form-item>
                <el-form-item prop="password">
                    <el-input type="password" placeholder="password" v-model="ruleForm.password" @keyup.enter.native="submitForm('ruleForm')">admin</el-input>
                </el-form-item>

                <el-form-item prop="loginType">
                    <el-select v-model="value" placeholder="请选择" style="width:300px">
                        <el-option
                          v-for="item in options"
                          :key="item.value"
                          :label="item.label"
                          :value="item.value">
                        </el-option>
                    </el-select>
                </el-form-item>
                <div class="login-btn">
                    <el-button type="primary" @click="submitForm('ruleForm')">登录</el-button>
                </div>
                <p style="font-size:12px;line-height:30px;color:#999;">Tips : 用户名和密码随便填。</p>
            </el-form>
        </div>


        <div>
        <br/>
         <img :src="'http://localhost:8011/qr/encode/apkDownload'">
         <br/>
         <div style="align:center">
         &nbsp;&nbsp;&nbsp;&nbsp;<span style="color:red">下载Android客户端</span>
        </div>
        </div>
    </div>
</template>

<script>
    export default {
        data: function(){
            return {
                ruleForm: {
                    username: '',
                    password: ''
                },
                rules: {
                    username: [
                        { required: true, message: '请输入用户名', trigger: 'blur' }
                    ],
                    password: [
                        { required: true, message: '请输入密码', trigger: 'blur' }
                    ]
                },
                options: [{
                    value: 'system',
                    label: '系统管理员'
                }, {
                    value: 'enviroment',
                    label: '环卫管理员'
                }],
                value: "system"
            }

           
          
        },
        methods: {
            submitForm(formName) {
                const self = this;
                self.$refs[formName].validate((valid) => {
                    if (valid) {
                        localStorage.setItem('ms_username',self.ruleForm.username);
                        if("系统管理员"==self.value ||"system"==self.value){
                            localStorage.setItem('ms_title',"后台管理系统");
                            self.$router.push('/sysMonitor');
                        }else if("环卫管理员"==self.value || "enviroment"== self.value){
                            localStorage.setItem('ms_title',"环卫后台管理系统");
                            self.$router.push('/envMonitor');
                        }else 
                            self.$router.push('/home');
                    } else {
                        return false;
                    }
                });
            }
        }
    }
</script>

<style scoped>
    .login-wrap{
        position: relative;
        width:100%;
        height:100%;
    }
    .ms-title{
        position: absolute;
        top:50%;
        width:100%;
        margin-top: -230px;
        text-align: center;
        font-size:30px;
        color: #fff;

    }
    .ms-login{
        position: absolute;
        left:50%;
        top:50%;
        width:300px;
        height:200px;
        margin:-150px 0 0 -190px;
        padding:40px;
        border-radius: 5px;
        background: #fff;
    }
    .login-btn{
        text-align: center;
    }
    .login-btn button{
        width:100%;
        height:36px;
    }
</style>