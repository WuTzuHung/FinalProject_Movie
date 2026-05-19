<script>
import { RouterLink, RouterView } from 'vue-router'
import { mapState,mapActions } from 'pinia';
import Cookies from 'js-cookie'
import auth from '../store/auth';
export default{
    data(){
        return{
            login:'',
            loginAccount:"AA123",
            userLoggedIn:false,
            account:"",
        }
    },
    computed:{
        ...mapState(auth, ["getAuth","getuser"])
    },
    components:{
        RouterLink,
    },
    methods:{
        ...mapActions(auth,["login","logout"]),
        loga(){
            this.$router.push("/login")
        },
        logincheck(){
            this.userLoggedIn = Cookies.get('userLoggedIn') === 'true'
            if (this.userLoggedIn) {
                this.loginAccount = Cookies.get('account')
                Cookies.set('userLoggedIn', true, { expires: 7, path: '/' });
                Cookies.set('account', this.loginAccount, { expires: 7, path: '/' });
            }
            console.log(this.userLoggedIn)
        },
        logout1(){
            Cookies.remove('userLoggedIn');
            Cookies.remove('account');
            this.userLoggedIn =false
            this.loginAccount =""
            this.logout()
            this.$router.push("/login")
        },
        backuserp(){
            this.$router.push("/backuser")
        },
    },
    mounted(){
        this.logincheck();
        this.$watch(() => Cookies.get('userLoggedIn'), (newVal) => {
            this.userLoggedIn = newVal === 'true';
        });
    }
}
</script>

<template>
    <div class="headerShow">
        <div class="box">
            <RouterLink to="/" class="a">首頁</RouterLink>
            <RouterLink to="/moviecomment" class="a" style="display: none;">留言區</RouterLink>
            <RouterLink to="/ticket" class="a">購票</RouterLink>
            <RouterLink :to="`/mypage`" class="a">個人主頁</RouterLink>
            <RouterLink :to="`/create`" class="a">影迷創作</RouterLink>
            <div v-if="this.userLoggedIn || this.getAuth" class="a">
                <p v-if="this.userLoggedIn" @click="backuserp()">登入帳號：{{ this.loginAccount }}</p>
                <p v-if="this.getAuth" @click="backuserp()">登入帳號：{{ this.getuser }}</p>
            </div>
            <div v-if="this.userLoggedIn || this.getAuth" class="a">
                <p @click="logout1">登出</p>
            </div>
            <div v-if="(this.getAuth !== this.userLoggedIn) == false" class="a" style="">
                <p style="margin: 0;" @click="loga">登入</p>
            </div>
        </div>
    </div>
</template>

<style scoped lang="scss">

.headerShow{
    width: 100%;
    min-height: var(--app-header-height, 72px);
    position: fixed;
    top: 0;
    left: 0;
    z-index: 1000;
    background-color: rgba(82, 95, 117, 0.96);
    box-shadow: 0 10px 24px rgba(25, 31, 43, 0.18);
    backdrop-filter: blur(10px);
    .box{
        display:flex;
        text-align: center;
        justify-content: center;
        align-items: center;
        gap: clamp(8px, 1.4vw, 24px);
        width: min(100%, 1180px);
        min-height: var(--app-header-height, 72px);
        margin: 0 auto;
        padding: 0 24px;
        .a{
            font-family: "jf-openhuninn-2.0";
            width: auto;
            min-width: max-content;
            margin: 0;
            padding: 10px clamp(10px, 1.4vw, 18px);
            font-size: clamp(1rem, 1.45vw, 1.45rem);
            line-height: 1.2;
            text-decoration: none;
            white-space:nowrap;
            transition: 0.2s;
            color: whitesmoke;
            border-radius: 999px;
            cursor: pointer;
            p {
                margin: 0;
            }
            &:hover{
                background-color: rgba(245, 245, 245, 0.92);
                color:darkslategray;
            }
        }
    }
}

@media (max-width: 991px) {

    .headerShow{
        
    .box{
        justify-content: flex-start;
        overflow-x: auto;
        scrollbar-width: none;
        padding: 0 14px;

        .a{
            // width: 35%;
            flex: 0 0 auto;
            width: auto;
            font-size: 1em;
            padding: 9px 12px;
            // margin-top: 2.5dvh;
            // margin-bottom: 1.5dvh;
            justify-content: space-between;
            align-items: center;
            
            &:hover{
                background-color: gainsboro;
                color:darkslategray;
                // transform: none; /* 不使用缩放 */
                text-align: center; /* 文字居中 */
            }
        }
    }
}
}

@media (max-width: 575px) {
    .headerShow {
        .box {
            min-height: var(--app-header-height, 64px);

            .a {
                font-size: 0.92rem;
                padding: 8px 10px;
            }
        }
    }
}

</style>
