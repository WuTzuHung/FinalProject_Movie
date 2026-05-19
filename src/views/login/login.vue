<script>
import { mapState,mapActions } from 'pinia'
import { RouterLink } from "vue-router";
import Cookies from 'js-cookie'
import auth from '../../store/auth';
import Popper from "vue3-popper";
import Swal from 'sweetalert2'
export default {
  data() {
    return{
      cBox:false,
      account:"",
      password:"",
      accountW:"",
      passwordW:"",
      setacc:"",
      setpas:"",
      accall:[],
      email:"",
      show:0,
      showpwd:0,
      verify:"",
      cat:{},
      b:"", //修改彈跳視窗
    }
  },
  computed:{
    ...mapState(auth, ["getAuth","getuser"])
  },
  components: {
    RouterLink,
    Popper,
    Swal,
  },
  methods:{
    ...mapActions(auth,["login","logout"]),
    log(){
      if(this.cBox == true){
        localStorage.setItem("keep","keep")
        localStorage.setItem("setacc",this.account)
        localStorage.setItem("setpas",this.password)
      }
      if(this.account !="" && this.password !=""){
        fetch('https://spintbootmovie.zeabur.app/movie/user/login', {
            method: 'POST', // 這裡使用POST方法，因為後端是@PostMapping
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
              account:this.account,
              password:this.password,
            })
        })
        .then(response => response.json())
        .then(data => {
        // 處理返回的數據
            console.log(data)
            console.log(data.code)
            if(data.code == 201){
              Cookies.set('userLoggedIn', true, { expires: 7, path: '/' });
              Cookies.set('account', this.account, { expires: 7, path: '/' });
              // Cookies.set('admin', true, { expires: 7, path: '/' });
              this.login(this.account)
              Swal.fire('登入成功，歡迎管理者');
              this.$router.push("/")
            }
            if(data.code == 200){
              Cookies.set('userLoggedIn', true, { expires: 7, path: '/' });
              Cookies.set('account', this.account, { expires: 7, path: '/' });
              this.login(this.account)
              // console.log(this.account)
              console.log(Cookies.get('account'))
              console.log(this.getAuth)
              console.log(this.getuser)
              Swal.fire('登入成功');
              this.$router.push("/")
            }
            if(data.rtnCode == "Account not verify"){
              this.b = "帳號沒有驗證，請去註冊驗證"
            }
        })
        .catch(error => {
            console.error('Error fetching data:', error);
        });
      } else{
        this.b = "請輸入帳號密碼"
      }
    },
    clickC(){
      let e = document.getElementsByName("eye")
      let acc = document.getElementById("acc")
      if(e.class == "fa-solid fa-eye fa-lg eye"){
        e.class="fa-solid fa-eye-slash fa-lg eye"
        acc.type="text"
        this.show = 1
      } else{
        e.class="fa-solid fa-eye fa-lg eye"
        acc.type="password"
        this.show = 0
      }
    },
    register(){
        this.$router.push("/register")
    },
    forgetpwd(){
        fetch('https://spintbootmovie.zeabur.app/movie/user/forgetpwd', {
            method: 'POST', // 這裡使用POST方法，因為後端是@PostMapping
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
              email:this.email,
            })
        })
        .then(response => response.json())
        .then(data => {
        // 處理返回的數據
            console.log(data)
            console.log(data.code)
            if(data.code == 200){
              this.showpwd = 1
            }
            if(data.rtnCode == "Account not verify"){
              this.b = "請輸入信箱"
            }
        })
        .catch(error => {
            console.error('Error fetching data:', error);
        });
    },
    verifyway(){
      fetch('https://spintbootmovie.zeabur.app/movie/user/verifypwdAccount', {
            method: 'POST', // 這裡使用POST方法，因為後端是@PostMapping
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
              account:this.accountW,
              newPassword:this.passwordW,
              verificationCode:this.verify,
            })
        })
        .then(response => response.json())
        .then(data => {
        // 處理返回的數據
            console.log(data)
            console.log(data.code)
            if(data.code == 200){
              Swal.fire('修改完成');
            }
        })
        .catch(error => {
            console.error('Error fetching data:', error);
        });
    },
  },
  mounted(){
    if(localStorage.getItem("keep") == "keep"){
      this.account =localStorage.getItem("setacc")
      this.password =localStorage.getItem("setpas")
    localStorage.removeItem("keep")
    localStorage.removeItem("setacc")
    localStorage.removeItem("setpas")
  }
}
};
</script>

<template>
    <div class="cBox">
        <div class="box">
            <p class="textT">這裡是登入</p>
            <p class="textL">帳號</p>
            <div class="form-floating mb-3">
                <input type="text" class="form-control tb" id="floatingInput" placeholder="" v-model="this.account">
                <label class="tbc" for="floatingInput">請在這裡輸入帳號</label>
            </div>
            <p class="textL">密碼</p>
            <div class="form-floating mb-3">
                <input type="password" class="form-control tbp" id="acc" placeholder="" v-model="this.password">
                <i v-if="this.show == 0" class="fa-solid fa-eye fa-lg eye" @click="this.clickC()" name="eye"></i>
                <i v-if="this.show == 1" class="fa-solid fa-eye-slash fa-lg eye" @click="this.clickC()" name="eye"></i>
                <label class="tbc" for="floatingInput">請在這裡輸入密碼</label>
            </div>
            <div class="checkbox">
                <input class="leftC" type="checkbox" name="" id="cBox" v-model="cBox">
                <p class="textC">保留我的登入資訊</p>
            </div>
            <div class="logbox" >
              <button type="button" class="button" @click="register">註冊帳號</button>
              <button type="button" class="button" data-bs-toggle="modal" data-bs-target="#additem">忘記帳號</button>
              <Popper arrow placement="top" class="root login-popper" :offsetDistance="0" :content="this.b">
                  <button type="button" class="buttonA" @click="log()">登入</button>
              </Popper>
            </div>
            <div class="modal fade" id="additem" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered">
              <div class="modal-content">
                <div class="modal-header">
                  <h5 class="modal-title a" id="exampleModalLabel">請輸入驗證碼</h5>
                  <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                  <p class="textall">信箱</p>
                  <div class="form-floating mb-3">
                    <input type="text" class="form-control tb" id="floatingInput" placeholder="name@example.com" v-model="this.email" :disabled="this.showpwd==1">
                    <label class="tbc" for="floatingInput">請在這裡輸入信箱</label>
                  </div>
                  <div class="show" v-if="showpwd ==1">
                    <p class="textall">帳號</p>
                    <div class="form-floating mb-3">
                      <input type="text" class="form-control tb" id="floatingInput" placeholder="name@example.com" v-model="this.accountW">
                      <label class="tbc" for="floatingInput">請在這裡輸入帳號</label>
                    </div>
                    <p class="textall">新密碼</p>
                    <div class="form-floating mb-3">
                      <input type="text" class="form-control tb" id="floatingInput" placeholder="name@example.com" v-model="this.passwordW">
                      <label class="tbc" for="floatingInput">請在這裡輸入新密碼</label>
                    </div>
                    <p class="textall">驗整碼</p>
                    <div class="form-floating mb-3">
                      <input type="text" class="form-control tb" id="floatingInput" placeholder="" v-model="this.verify">
                      <label class="tbc" for="floatingInput">在這裡輸入驗整碼</label>
                    </div>
                  </div>
                </div>
                <div class="modal-footer" style="justify-content: space-around;">
                  <button type="button" class="btn btn-primary a" style="background-color: green;border: none;" @click="forgetpwd" :disabled="this.email.length <=7 || this.showpwd==1">送出驗證碼</button>
                  <button type="button" class="btn btn-primary a" data-bs-dismiss="modal" style="background-color: green;border: none;" @click="verifyway" :disabled="this.verify.length <=7 || this.accountW =='' || this.passwordW ==''">驗證</button>
                </div>
              </div>
            </div>
          </div>
        </div>
    </div>
</template>

<style scoped lang="scss">
.cBox {
  width: 100%;
  min-height: 92dvh;
  padding: clamp(24px, 4vw, 48px) 16px;
  text-align: center;
  display: flex;
  align-items: center;
  justify-content: center;
  background-image: linear-gradient(rgba(14, 20, 32, 0.35), rgba(14, 20, 32, 0.45)), url(../../picture/Movie.jpg);
  background-repeat: no-repeat;
  background-position: center;
  background-size: cover;

  .box {
    width: min(100%, 520px);
    min-height: auto;
    padding: clamp(28px, 4vw, 44px) clamp(20px, 4vw, 42px);
    align-self: center;
    align-items: center;
    background-color: rgba(82, 95, 117, 0.94);
    border-radius: 15px;
    box-shadow: 0 20px 50px rgba(0, 0, 0, 0.35);

    .textT {
      font-family: 'jf-openhuninn-2.0';
      font-size: clamp(1.7rem, 2.3vw, 2.25rem);
      margin: 0 0 28px;
      color: white;
    }

    .textL {
      font-family: 'jf-openhuninn-2.0';
      font-size: clamp(1.1rem, 1.45vw, 1.35rem);
      text-align: start;
      width: 100%;
      max-width: 400px;
      margin: 0 auto 8px;
      color: white;
    }

    .form-floating {
      width: 100%;
      max-width: 400px;
      margin-left: auto;
      margin-right: auto;
      position: relative;
    }

    .tb,
    .tbp {
      width: 100%;
      min-height: 54px;
      margin: 0 auto;
      padding-right: 48px;
    }

    .eye {
      position: absolute;
      top: 50%;
      right: 16px;
      z-index: 5;
      transform: translateY(-50%);
      cursor: pointer;
      transition: 0.3s;

      &:hover {
        color: rgb(255, 173, 65);
      }
    }

    .tbc {
      font-family: 'jf-openhuninn-2.0';
      font-size: 1rem;
      margin-left: 0;
      max-width: calc(100% - 24px);
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
    }

    .checkbox {
      width: 100%;
      max-width: 400px;
      margin: 4px auto 18px;
      align-items: center;
      display: flex;
      gap: 8px;
      text-align: left;

      .leftC {
        flex: 0 0 auto;
        margin: 0;
      }

      .textC {
        margin: 0;
        color: white;
        font-size: 0.98rem;
      }
    }

    .logbox {
      width: 100%;
      max-width: 420px;
      margin: 6px auto 0;
      display: grid;
      grid-template-columns: repeat(3, minmax(0, 1fr));
      gap: 12px;
      align-items: stretch;

      .button,
      .buttonA {
        width: 100%;
        height: 48px;
        min-height: 48px;
        border: none;
        background-color: rgb(176, 182, 213);
        border-radius: 10px;
        font-size: clamp(1rem, 1.4vw, 1.25rem);
        font-family: 'jf-openhuninn-2.0';
        line-height: 1.2;
        padding: 8px 10px;
        box-sizing: border-box;
        display: flex;
        align-items: center;
        justify-content: center;
        transition: 0.2s;

        &:hover {
          background-color: rgb(198, 203, 229);
        }
      }
    }
  }
}

.root {
  width: 100%;
  min-width: 0;
  --popper-theme-background-color: #333333;
  --popper-theme-background-color-hover: #333333;
  --popper-theme-text-color: #ffffff;
  --popper-theme-border-width: 0px;
  --popper-theme-border-style: solid;
  --popper-theme-border-radius: 6px;
  --popper-theme-padding: 18px;
  --popper-theme-box-shadow: 0 6px 30px -6px rgba(0, 0, 0, 0.25);
  margin: 0;
}

:global(.login-popper.inline-block) {
  display: block !important;
  width: 100%;
  min-width: 0;
}

.login-popper :deep(> div:first-child) {
  width: 100%;
}

.login-popper :deep(.buttonA) {
  width: 100%;
}

.modal-dialog {
  max-width: min(92vw, 520px);
}

.modal-body {
  .form-floating,
  .tb {
    width: 100%;
  }
}

.textall {
  margin-bottom: 8px;
  text-align: left;
}

@media (max-width: 991px) {
  .cBox {
    .box {
      width: min(100%, 460px);

      .logbox {
        grid-template-columns: 1fr;

        > .button,
        > .login-popper {
          width: 100%;
          max-width: 100%;
          justify-self: stretch;
        }
      }
    }
  }
}

@media (max-width: 767px) {
  .cBox {
    min-height: 92dvh;
    padding: 20px 14px 32px;
    align-items: flex-start;

    .box {
      width: 100%;
      padding: 24px 16px;
      margin: 0;
      border-radius: 12px;

      .textT {
        font-size: 1.55rem;
        margin-bottom: 22px;
      }

      .textL {
        font-size: 1.05rem;
      }

      .tb,
      .tbp {
        min-height: 52px;
      }

      .tbc {
        font-size: 0.92rem;
      }

      .checkbox {
        margin-bottom: 16px;

        .textC {
          font-size: 0.92rem;
        }
      }

      .logbox {
        gap: 10px;

        .button,
        .buttonA {
          width: 100%;
          height: 46px;
          min-height: 46px;
          font-size: 1rem;
        }
      }
    }
  }

  .root {
    .buttonA {
      height: 46px;
      min-height: 46px;
      font-size: 1rem;
    }
  }
}
</style>
