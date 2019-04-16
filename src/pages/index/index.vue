<template>
  <div>

    <div class="userinfo">
      <img class="userinfo-avatar" v-if="userInfo.avatarUrl" :src="userInfo.avatarUrl" background-size="cover" />
      <img class="userinfo-avatar" src="/static/images/user.png" background-size="cover" />

      <div class="userinfo-nickname">
        <card :text="userInfo.nickName"></card>
      </div>
    </div>
    <button @click="weixinLogin">微信登录</button>
    <!-- 微信开发用bindgetuserinfo vue用@getuserinfo -->
    <button open-type="getUserInfo" @getuserinfo="getUserInfo">获取用户信息</button>
    <button open-type="openSetting">打开授权设置页</button>
    <!-- <open-data type="groupName" open-gid="xxxxxx"></open-data> -->
    <open-data type="userNickName"></open-data>
    <open-data type="userAvatarUrl"></open-data>
    <open-data type="userGender" lang="zh_CN"></open-data>
  </div>
</template>

<script>
import card from '@/components/card'

export default {
  data () {
    return {
      motto: 'Hello miniprograme',
      userInfo: {
        nickName: 'mpvue',
        avatarUrl: 'http://mpvue.com/assets/logo.png'
      },
      noGetUserInfoAuth: false
    }
  },

  components: {
    card
  },
  onShow () {
    console.log('S')
    this.getSetting()
  },
  computed: {

  },
  methods: {
    getSetting () {
      wx.getSetting({
        success: (res) => {
          if (res.authSetting['scope.userInfo']) {
            wx.getUserInfo({
              success: (res) => {
                console.log('已授权', res.userInfo)
                this.noGetUserInfoAuth = false
              }
            })
          } else {
            this.noGetUserInfoAuth = true
            console.log('未授权')
          }
        }
      })
    },
    weixinLogin () {
      wx.login({
        success: (res) => {
          console.log('res', res)
          wx.getSetting({
            success: (res) => {
              console.log('setting', res)
              if (res.authSetting['scope.userInfo']) {
                wx.getUserInfo({
                  success: (res) => {
                    console.log('userInfo', res)
                    this.userInfo = res.userInfo
                  }
                })
              } else {
                console.log('没有授权获取用户信息')
              }
            }
          })
        }
      })
    },

    /**
       * 该接口方法不能获取openId,unionId,根据官方文档，openId，unionId都交给开发者服务器处理
       * 小程序登录文档https://developers.weixin.qq.com/miniprogram/dev/framework/open-ability/login.html
       * 说明：
       *   1、调用 wx.login() 获取 临时登录凭证code ，并回传到开发者服务器。
       *   2、调用 auth.code2Session 接口，换取 用户唯一标识 OpenID 和 会话密钥 session_key。
       * 之后开发者服务器可以根据用户标识来生成自定义登录态，用于后续业务逻辑中前后端交互时识别用户身份。
       * 注意：
       * 会话密钥 session_key 是对用户数据进行 加密签名 的密钥。为了应用自身的数据安全，开发者服务器不应该把会话密钥下发到小程序，也不应该对外提供这个密钥。
       * 2、临时登录凭证 code 只能使用一次
       */
    getUserInfo (e) {
      // 缺少openId,unionId
      console.log(e.mp.detail)
    },
    canIUse (method) {
      return wx.canIUse(method)
    },
    doLogin () {
      console.log('doLogin')
    }
  },

  created () {
    // let app = getApp()
  }
}
</script>

<style scoped>
.userinfo {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.userinfo-avatar {
  width: 128rpx;
  height: 128rpx;
  margin: 20rpx;
  border-radius: 50%;
}

.userinfo-nickname {
  color: #aaa;
}

</style>
