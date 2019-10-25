<template>
    <div class=" box">
        <h1 class="miantitle">登录</h1>
        <div class="loginbox">
            <div class="ipt">
                <input class="username " v-model="cell" placeholder="请输入手机号码"/>
                <i-icon type="mobilephone" size="26" color="#ACACAC" class="usericon"/>
            </div>
            <div class="ipt">
                <input class="password " v-model="authcode" placeholder="验证码" style="width:50%;"/>
                <i-icon type="lock" size="26" color="#ACACAC" class="usericon"/>
                <button @click="getcode" class="getcode" :disabled="btnable">{{codeText}}</button>
            </div>
            <!--            <div class="ipt">-->
            <!--                <input class="password " v-model="psw" type="password" placeholder="请输入密码"/>-->
            <!--                <i-icon type="lock" size="26" color="#ACACAC" class="usericon"/>-->
            <!--            </div>-->
            <button @click="handleClick" class="enter">登录</button>
            <!--            <p class="spans">-->
            <!--                <span class="sp1" @click="register">注册</span>-->
            <!--                <span class="sp2" @click="pswreset">忘记密码？</span>-->
            <!--            </p>-->
        </div>
        <i-toast id="toast"/>
    </div>
</template>
<script>
    import {$Toast} from '../../../static/iview/base/index'

    export default {
        data() {
            return {
                btnable: "",
                cell: '',
                psw: '',
                authcode: '',
                codeText: "获取验证码",
                openid: ""
            }
        },
        components: {},
        methods: {
            getcode() {
                var _this = this
                if (this.cell == "") {
                    $Toast({
                        content: '请输入手机号码',
                        type: 'warning'
                    });
                } else {

                    //提交
                    wx.request({
                        url: 'https://hd.xmountguan.com/railway/other.aspx?func=SendSms&mobile=' + _this.cell,
                        success(res) {
                            if (res.data.success = "success") {
                                $Toast({
                                    content: '验证码已发送',
                                    type: 'warning'
                                });
                                // this.disabled = "disabled"
                                _this.btnable = "disabled";
                                console.log('You Just Fucked getcode');
                                const TIME_COUNT = 60;
                                if (!_this.timer) {
                                    _this.count = TIME_COUNT;
                                    _this.timer = setInterval(() => {
                                        if (_this.count > 0 && _this.count <= TIME_COUNT) {
                                            _this.count--;
                                            _this.codeText = _this.count + 's后重新获取'
                                        } else {
                                            _this.btnable = "",
                                                clearInterval(_this.timer);
                                            _this.codeText = "获取验证码"
                                            _this.timer = null;
                                        }
                                    }, 1000)
                                }
                            }
                        },
                        fail() {
                            console.log('网络错误')
                            $Toast({
                                content: '网络错误，请稍后重试',
                                type: 'warning'
                            });
                        }
                    })
                }
            }
            ,
            register() {
                mpvue.navigateTo({
                    url: '../register/main',
                })
            },
            pswreset() {
                console.log('You Just Fucked pswreset');
                mpvue.navigateTo({
                    url: '../pswreset/main',

                })
            },
            handleClick() {
                console.log(this.cell)
                var wx = mpvue;

                wx.request({
                    url: 'https://hd.xmountguan.com/railway/user.aspx?func=login_byvcode&mobile=' + this.cell + '&openid=' + this.openid + "&vcode=" + this.authcode,   //仅为示例，并非真实的接口地址
                    success(res) {
                        console.log(res.data)
                        if (res.data.UID) {
                            wx.setStorageSync("UID", res.data.UID);
                            wx.setStorageSync("Mobile", res.data.Mobile);
                            wx.setStorageSync("UserName", res.data.UserName);
                            wx.setStorageSync("Role", res.data.Role);
                            wx.setStorageSync("DName", res.data.DName);
                            wx.setStorageSync("WName", res.data.WName);
                            const url = '../indexswiper/main'
                            mpvue.navigateTo({url})
                        } else {
                            $Toast({
                                content: '账号或验证码错误！',
                                type: 'warning'
                            });
                        }
                    },
                    fail() {
                        $Toast({
                            content: '请稍候重试',
                            type: 'warning'
                        });
                    }
                })


            },
            handleWarning() {
                $Toast({
                    content: '警告的提示',
                    type: 'warning'
                });
            },
        },
        mounted() {
            console.log(this.$root.$mp.appOptions)
            console.log(this.$root.$mp.query)
            this.openid = wx.getStorageSync('openid')
        },

        onShow() {
            wx.showShareMenu();
        }
    }

</script>
<style scoped>
    .miantitle {
        width: 100%;
        display: block;
        text-align: center;
        font-size: 35rpx;
        position: absolute;
        top: 22rpx;
        left: 0;

    }

    .box {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        width: 100%;
        height: 100vh;
        /*background      : #acacac;*/
    }

    .row {
        width: 100%;
        height: 500rpx;
        display: block;
    }

    .loginbox {
        width: 500rpx;
        height: 540rpx;
        text-align: center;
        position: relative;

    }

    .ipt {
        width: 100%;
        height: 75rpx;
        line-height: 75rpx;
        font-size: 22rpx;
        background: white;
        border-radius: 18rpx;
        text-align: left;
        border: 2rpx solid #b1b1b1;
        position: relative;
        margin-bottom: 50rpx;


    }

    .ipt input {
        width: 88%;
        height: 75rpx;
        margin-left: 12%;
        line-height: 75rpx;
        display: block;
        font-size: 25rpx;


    }

    .usericon {
        position: absolute;
        left: 5rpx;
        top: -1rpx;
        font-size: 18rpx;
    }

    .password {
        /*margin-top : 10rpx;*/
    }

    .enter {
        width: 100%;
        /*height: 50rpx;*/
        background: #2d8cf0;
        position: relative;
        color: white;
        margin-top: 115rpx;
        height: 72rpx;
        line-height: 72rpx;
        font-size: 28rpx;
    }

    .spans {
        width: 100%;
        height: 10rpx;
        font-size: 20rpx;
        margin-top: 25rpx;
    }

    .sp1 {
        float: left;
        color: #2d8cf0;
    }

    .sp2 {
        float: right;
    }

    .usericon {
        position: absolute;
        left: 5rpx;
        top: -1rpx;
        font-size: 18rpx;
    }

    .getcode {
        width: 185rpx;
        height: 66rpx;
        display: block;
        float: right;
        position: absolute;
        top: 4rpx;
        right: 10rpx;
        background: #2d8cf0;
        line-height: 65rpx;
        color: white;
        font-size: 26rpx;
        padding: 0;
        box-sizing: border-box;

    }
</style>
