<template>
  <form class="Inputform" @submit.prevent="register">
    <div class="form-body">

      <span>驴友，请注册</span>
      <input type="text" class="form-input" name="username" placeholder="账号" v-model="account.userId">
      <input type="password" class="form-input" name="password" placeholder="密码" v-model="account.password">
      <input type="password" class="form-input" name="password" placeholder="重复密码" v-model="repeatPassword">
      <div class="form-button">
        <input type="submit" class="form-submit">
        <router-link to="/accountForm/login" class="form-return"><span>进行登录</span></router-link>
      </div>
    </div>
  </form>
</template>

<script>
  import common from '../../../common/common';

  export default {
    name: "register",
    data() {
      return {
        account: {
          userId: '',
          password: ''
        },
        repeatPassword: ''
      };
    },
    methods: {
      async register() {
        if(this.account.userId.trim().length * this.account.password.trim().length === 0){
          this.$Message.warning({
            content: "账号或者密码不能为空!",
            duration: 5
          })
        }else{
          var that = this;
          if (this.account.password === this.repeatPassword) {
            // 判断用户是否存在

            if (await this.isExist()) {
              this.$Message.warning({
                content: "该用户已存在！",
                duration: 5
              });
            } else {
              var url = common.apidomain + "/accounts";

              this.$http.post(url,this.account).then(function (response) {
                var data = response.data;
                if (data.status === 0) {
                  that.$Message.success({
                    content: data.message,
                    duration: 5
                  });

                  //若注册成功清空表单
                  that.account.userId = '';
                  that.account.password = '';
                  that.repeatPassword = '';

                } else {
                  that.$Message.warning({
                    content: data.message,
                    duration: 5
                  });
                }
              }).catch(function (error) {
                that.$Message.warning({
                  content: error,
                  duration: 5
                });
              });
            }
          } else {
            that.$Message.warning({
              content: "两次密码不匹配",
              duration: 5
            });
          }
        }
      },
      async isExist() {
        // 这里需要用 async 和 await 关键字去阻塞 ajax 的执行顺序
        var url = common.apidomain + "/accounts?userId=" + this.account.userId;
        await this.$http.get(url).then(function (response) {
          var data = response.data;
          if (data.status === 1) {
            return true;
          } else {

            return false;
          }
        })
      }
    }
  }
</script>

<style scoped>
  .Inputform {
    /*width: 100%;*/
    /*display: inline-block;*/
    height: 500px;
    background: #fff;
    border-radius: 5px;
    box-shadow: -4px 7px 46px 2px rgba(0, 0, 0, 0.1);
    overflow: hidden;
  }

  .Inputform > .form-body {
    padding: 30px;
  }

  .Inputform > .form-body > span {
    display: inline-block;
    text-transform: uppercase;
    letter-spacing: 2px;
    font-size: 20px;
    margin: 0 0 30px 0;
    color: #000;
  }

  .Inputform .form-input {
    display: block;
    width: 90%;
    font-size: 16px;
    font-weight: 300;
    height: 50px;
    margin-bottom: 30px;
    border: none;
    border-bottom: 1px solid rgba(0, 0, 0, 0.1);
    box-shadow: none;
    border-radius: 0px;
    transition: all 0.3s ease;
    outline: none;
  }

  .Inputform .form-group {
    margin-bottom: 30px;
  }

  .Inputform .form-group > label {
    font-family: "Open Sans", Arial, sans-serif;
    line-height: 1.5;
    font-size: 14px;
    font-weight: 300;
  }

  .Inputform .form-group > label > input {
    margin: 4px 0 0;

    line-height: normal;
  }

  .Inputform .form-group > p {
    font-size: 16px;
    color: #9f9f9f;
    font-weight: 300;
  }

  .Inputform .form-group > p > a {
    color: #000000
  }

  .Inputform .form-group > p > a:hover {
    color: #f74133;
  }

  .Inputform .form-submit {
    display: inline-block;
    font-family: "Open Sans", Arial, sans-serif;
    line-height: 1.5;
    font-size: 16px;
    height: 50px;
    border-radius: 4px;
    -webkit-appearance: button;
    cursor: pointer;
    padding-right: 20px;
    padding-left: 20px;
    border: none;
    background: #33cccc;
    color: #ffffff;
    box-shadow: -2px 10px 20px -1px rgba(51, 204, 204, 0.4);
    margin-top: 2px;
  }

  .Inputform .form-button {
    position: relative;
  }

  .Inputform .form-return {
    display: inline-block;
    font-family: "Open Sans", Arial, sans-serif;
    line-height: 1.5;
    font-size: 16px;
    height: 50px;
    border-radius: 4px;
    position: absolute;
    top: 0;
    cursor: pointer;
    padding-right: 20px;
    padding-left: 20px;
    margin-left: 60px;
    vertical-align: middle;
    border: 1px solid rgba(246, 246, 246, 0.4);
    background: #fff;
    background: -webkit-linear-gradient(#fff, #eee); /* Safari 5.1 - 6.0 */
    background: -o-linear-gradient(#fff, #eee); /* Opera 11.1 - 12.0 */
    background: -moz-linear-gradient(#fff, #eee); /* Firefox 3.6 - 15 */
    background: linear-gradient(#fff, #eee); /* 标准的语法 */
    color: #000000;
    box-shadow: -2px 10px 20px -1px rgba(0, 0, 0, 0.2);
  }

  .Inputform .form-return > span {
    display: inline-block;
    margin-top: 12px;
  }
</style>
