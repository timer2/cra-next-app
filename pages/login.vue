 <template>
    <div class="login-container">
        <el-form class="login-form" :model="ruleForm" :rules="rules" ref="loginForm" label-width="100px">
            <div class="logo-container">
                <img src="/logo.png" alt="">
            </div>
            <el-form-item prop="email" label="邮箱">
                <el-input v-model="ruleForm.email" placeholder="请输入邮箱"></el-input>
            </el-form-item>
            <el-form-item prop="captcha" label="验证码" class="captcha-container">
                <div class="captcha">
                    <img @click="fnUpdateCaptcha" :src="code.captchaUrl" alt="">
                </div>
                <el-input v-model="ruleForm.captcha" placeholder="请输入验证码"></el-input>
            </el-form-item>
            <el-form-item prop="emailCode" label="邮箱验证码" class="captcha-container">
                <div class="captcha">
                    <el-button @click="fnSendEmailCode" :disabled="send.timer > 0" type="primary">{{sendText}}</el-button>
                </div>
                <el-input v-model="ruleForm.emailCode" placeholder="请输入邮箱验证码"></el-input>
            </el-form-item>
            <el-form-item prop="passwd" label="密码">
                <el-input type="password" v-model="ruleForm.passwd" placeholder="请输入密码"></el-input>
            </el-form-item>

            <el-form-item>
                <el-button type="primary" @click="submitForm('loginForm')">登录</el-button>
            </el-form-item>
        </el-form>
    </div>
</template>
<script>
import md5 from "md5";
export default {
    layout: "login",
    data() {
        return {
            code: {
                captchaUrl: '',
            },
            send: {
                timer: 0
            },
            ruleForm: {
                email: '1@qq.com',
                passwd: '123456',
                captcha: '',
                emailCode: ''
            },
            rules: {
                email: [
                    {required: true, message: '请输入邮箱', trigger: 'blur'},
                    {type: 'email', message: '请输入正确的邮箱'}
                ],
                passwd: [
                    {required: true, pattern: /^[\w_-]{6,12}$/g, message: '请输入6到12位密码', trigger: 'blur'}
                ],
                captcha: [
                    {required: true, message: '请输入验证码', trigger: 'blur'}
                ],
                emailCode: [
                    {required: true, message: '请输入邮箱验证码', trigger: 'blur'}
                ]
            }
        }
    },
    computed: {
        sendText: function() {
            return this.send.timer <= 0 ? "发送" : `${this.send.timer}s后发送`
        }
    },
    methods: {
        async fnSendEmailCode() {
            let res = await this.$http.get('/sendCode?email='+this.ruleForm.email)
            this.send.timer = 10
            this.timer = setInterval(() => {
                this.send.timer --
                if (this.send.timer === 0) {
                    clearInterval(this.timer)
                }
            }, 1000)
        },
        fnUpdateCaptcha() {            
            this.code.captchaUrl = '/api/captcha?_t=' + new Date().getTime();
        },
        submitForm(formName) {
            this.$refs[formName].validate(async (valid) => {                
                if (valid) {
                    let data = {
                        email: this.ruleForm.email,
                        passwd: md5(this.ruleForm.passwd),
                        captcha: this.ruleForm.captcha,
                        emailCode: this.ruleForm.emailCode
                    }
                    let res = await this.$http.post("/user/login", data)                
                    if (res && res.success) {                                
                        // 返回token：是将包含登录名、过期时间等信息加了密
                        this.$message.success("登录成功")
                        localStorage.setItem("token", res.result.token)
                        this.$nextTick(() => {
                            this.$router.push("/")
                        }, 500)
                    }
                }
            });
        },
        resetForm(formName) {
            this.$refs[formName].resetFields();
        }
    },
    mounted() {        
        this.fnUpdateCaptcha();
    }
}
</script> 

<style lang="stylus">
.login-form
    width 800px
    margin 50px auto
</style>