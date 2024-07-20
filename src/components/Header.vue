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
    min-width: 100%;
    height: 8dvh;
    .box{
        display:flex;
        text-align: center;
        justify-content: center;
        min-width: 100%;
        height: 8dvh;
        background-color: #525f75;
        .a{
            font-family: "jf-openhuninn-2.0";
            width: 20%;
            font-size: 2em;
            text-decoration: none;
            white-space:nowrap;
            transition: 0.4s;
            color: whitesmoke;
            border-radius: 5px;
            &:hover{
                background-color: gainsboro;
                color:darkslategray;
                transform:scale(1.1,1.1);
            }
        }
    }
}

@media (max-width: 767px) {

    .headerShow{

    .box{

        .a{
            width: 35%;
            font-size: 1em;
            margin-top: 2dvh;
        }
    }
}
}

</style>