<template>
    <div>
        <!-- #707070 -->
        <vue-particles
                class="login-background"
                color="#97D0F2"
                :particleOpacity="0.7"
                :particlesNumber="50"
                shapeType="circle"
                :particleSize="4"
                linesColor="#97D0F2"
                :linesWidth="1"
                :lineLinked="true"
                :lineOpacity="0.4"
                :linesDistance="150"
                :moveSpeed="3"
                :hoverEffect="true"
                hoverMode="grab"
                :clickEffect="true"
                clickMode="push">
        </vue-particles>
        <div class="login-other">
            <el-form :rules="rules" ref="loginForm" :model="loginForm" class="loginContainer">
                <H3 class="loginTitle">人事管理系统</H3>
                <el-form-item prop="username">
                    <el-input prefix-icon="el-icon-user-solid" size="normal" type="text" v-model="loginForm.username"
                              auto-complete="off"
                              placeholder="请输入用户名"></el-input>
                </el-form-item>
                <el-form-item prop="password">
                    <el-input prefix-icon="el-icon-lock" size="normal" type="password" v-model="loginForm.password"
                              auto-complete="off"
                              placeholder="请输入密码"></el-input>
                </el-form-item>
                <el-form-item prop="code">
                    <div style="display: flex;justify-content: space-between">
                        <el-input prefix-icon="el-icon-circle-check" style="width: 230px" size="normal" type="text"
                                  v-model="loginForm.code" auto-complete="off"
                                  placeholder="请输入验证码" @keydown.enter.native="submitLogin">
                        </el-input>
                        <img :src="vcUrl" @click="updateVerifyCode">
                    </div>
                </el-form-item>
                <el-checkbox size="normal" class="loginRemember" v-model="checked">记住密码</el-checkbox>
                <el-button size="normal" type="primary" style="width: 100%;" @click="submitLogin">登录</el-button>
            </el-form>
        </div>
    </div>

</template>

<script>

    export default {
        name: "Login",
        data() {
            return {
                imgSrc: require('@/assets/logo.png'),
                vcUrl: '/vhr/verifyCode?time=' + new Date(),
                loginForm: {
                    username: 'admin',
                    password: '123',
                    code: ''
                },
                checked: true,
                rules: {
                    username: [{required: true, message: '请输入用户名', trigger: 'blur'}],
                    password: [{required: true, message: '请输入密码', trigger: 'blur'}],
                    code: [{required: true, message: '请输入验证码', trigger: 'blur'}]
                }
            }
        },
        methods: {
            //点击图片更新验证码
            updateVerifyCode() {
                this.vcUrl = '/vhr/verifyCode?time=' + new Date();
            },
            submitLogin() {
                this.$refs.loginForm.validate((valid) => {
                    if (valid) {
                        //alert('submit!');
                        this.postKeyValueRequest('/doLogin', this.loginForm).then(resp => {
                            if (resp) {
                                // 保存登录信息到sessionStorage，关闭页面信息会清除
                                window.sessionStorage.setItem("user", JSON.stringify(resp.obj));
                                // 重定向路径
                                let path = this.$route.query.redirect;
                                this.$router.replace((path == '/' || path == undefined) ? '/home' : path);
                            } else {
                                this.vcUrl = '/vhr/verifyCode?time=' + new Date();
                                this.loginForm.code = "";
                            }
                        })
                    } else {
                        this.$message.error('登录失败，请重新登录。');
                        return false;
                    }
                });
            }
        }
    }
</script>

<style>
    .login-background {
        background: linear-gradient(-180deg, #dcf2e6 0%, #ffffff 100%);
        width: 100%;
        height: 100%; /**宽高100%是为了图片铺满屏幕 */
        z-index: -1;
        position: absolute;
    }

    .login-other {
        z-index: 1;
        margin: 180px 0 0 calc(calc(100vw - 410px) / 2);
        position: absolute;
    }

    .loginContainer {
        border-radius: 15px;
        background-clip: padding-box;
        width: 350px;
        padding: 15px 35px 15px 35px;
        background: #fefefe;
        border: 1px solid #eaeaea;
        box-shadow: 0 0 25px #cac6c6;
    }

    .loginTitle {
        margin: 15px auto 20px auto;
        text-align: center;
        color: #707070;
    }

    .loginRemember {
        text-align: left;
        margin: 0 0 15px 0;
    }

</style>

