<template>
    <div class="bg" @click="formfalse"></div>
    <div class="overview" v-if="iscommit === 1">
        <overview></overview>
    </div>
    <div class="commitform" v-if="iscommit === 2">
        <Commit></Commit>
    </div>
    <div class="manageuser" v-if="iscommit === 3">
        <ManageUser></ManageUser>
    </div>
    <div class="header">
        CVE漏洞检索复现平台
    </div>

    <div class="apps2">
        <div class="choice" @click="showView">
            <div class="app-item">
                <img src="../assets/overlook.png" class="app-icon">
            </div>
            <div class="app-label">漏洞统计</div>
        </div>
        <div class="choice" @click="commitCVE" v-if="ulevel === 'admin' || ulevel === 'developer'">
            <div class="app-item">
                <img src="../assets/analysis.png" class="app-icon">
            </div>
            <div class="app-label">提交漏洞</div>
        </div>
        <div class="choice" @click="manageUser" v-if="ulevel === 'admin'">
            <div class="app-item">
                <img src="../assets/team.png" class="app-icon">
            </div>
            <div class="app-label">管理用户</div>
        </div>
    </div>
    <div class="user" @mouseenter="showDialog" @mouseleave="data.showLogout = false">
        用户: <span class="username">{{ user }}</span>
        <div class="dialog" v-if="data.showLogout">
            <span @click="exitbtn" style="cursor: pointer;">退出登录</span>
        </div>
    </div>
    <div class="container" @click="formfalse">
        <div class="title">S2Vulhub</div>
        <div class="searchbar">
            <img src="../assets/search.png" class="mg" @click="search">
            <input type="text" id="search" v-model="keyword" placeholder="搜索CVE编号" @keyup.enter="search">
            <img src="/cse.ico" class="mcp">
        </div>
    </div>


</template>

<script setup>
import { ref,reactive ,onMounted } from 'vue';
import { ElMessage } from "element-plus";
import { useRouter } from 'vue-router';
import Commit from '../components/commit.vue';
import overview from '../components/overview.vue';
import ManageUser from '../components/manageu.vue';
const router = useRouter();
const keyword = ref('');
const user = ref('');
const ulevel = ref('')
const iscommit = ref(0);
const data = reactive({
    showLogout: false,
})
const search = () => {
    if (keyword.value === '') {
        ElMessage.error('请输入CVE编号');
        return;
    }
    fetch(`/S2VulnHub/user_cve/${keyword.value}.json`)
        .then(res => {
            if (!res.ok) {
                ElMessage.error('未查询到该编号的漏洞信息');
                throw new Error(`HTTP error! status: ${res.status}`);
            }
            return res.json();
        })
        .then(data => {
            console.log(data);
            router.push({ path: '/home', query: { CVE: keyword.value } })
        }) .catch(error => {
            console.error('An error occurred:', error);
        });
}
function showDialog() {
    console.log("click")
    data.showLogout = true
}
const exitbtn = () => {
    router.push('/login');
    localStorage.setItem("isLoggedIn", 0);
}
const showView = () => {
    iscommit.value = 1;
}
const commitCVE = () => {
    console.log("click")
    iscommit.value = 2;
}
const manageUser = () => {
    iscommit.value = 3;
}
const formfalse = () => {
    iscommit.value = 0;
}

onMounted(() => {
    user.value = localStorage.getItem('username');
    if (user.value.length > 5) {
        user.value = user.value.slice(0, 4) + '...';
    }
    // console.log('uu',user.value)
    ulevel.value = localStorage.getItem('level');
    // console.log('u',ulevel.value)
})
</script>

<style scoped>
.bg{
    position: fixed;
    top:-10px;
    width: 100%;
    height: 100%;
    background-color: #fdf7fe;
}
.overview {
    position: fixed;
        top: 20%;
            left: 50%;
            transform: translate(-50%, 60px);
        width: 1200px;
        max-width: 80%;
        height: 600px;
        max-height: 80%;
        background-color: #fff;
        border-radius: 10px;
        box-shadow: 0 10px 10px rgba(0, 0, 0, 0.1);
        z-index: 999;
        overflow: hidden;
}
.commitform {
    position: fixed;
    top: 20%;
        left: 50%;
        transform: translate(-50%, 60px);
    width: 800px;
    max-width: 90%;
    height: 600px;
    max-height: 90%;
    background-color: #fff;
    border-radius: 10px;
    box-shadow: 0 10px 10px rgba(0, 0, 0, 0.1);
    z-index: 999;
    overflow: auto;
}

.manageuser{
    position: fixed;
    top: 20%;
    left: 50%;
    transform: translate(-50%,60px);
    width: 800px;
    max-width: 90%;
    height: 800px;
    max-height: 60%;
    background-color: #fff;
    border-radius: 10px;
    box-shadow: 0 10px 10px rgba(0, 0, 0, 0.1);
    z-index: 999;
    overflow: hidden;
}

.user {
    position: absolute;
    font-size: 18px;
    top: 10px;
    right: 10px;
    color: #333;
    background-color: #f5f5f5;
    padding: 10px;
    border-radius: 5px;
    width: fit-content;
}

.username {
    color: #007BFF;
    font-weight: bold;
}
.header{
    position: absolute;
    font-size: 30px;
    color: #665390;
    text-align: center;

}
.container {
    position: absolute;
    top:20%;
    left: 50%;
    transform: translate(-50%, 0);
    width: 100%;
    height: 100px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}
.title{
    font-size: 60px;
    color: #409EFF;
    margin-top: 20px;
    margin-bottom: 30px;
    flex: 1;
}
.searchbar {
    border: rgb(218, 218, 218) solid 1px;
    border-radius: 2em;
    width: 552px;
    box-shadow: 0px 0px 5px rgb(212, 212, 212);
    margin: 0 auto;
    flex: 1;
}

#search {
    width: 440px;
    height: 40px;
    border: none;
    outline: none;
    font-size: 16px;
    color: rgb(112, 112, 112);
    margin-left: 10px;
}

.mcp {
    height: 35px;
}

.mg {
    height: 25px;
    margin-left: 15px;
}

#search,
.mcp,
.mg {
    vertical-align: middle;
}
:hover .mg{
    cursor: pointer;
}
.dialog {
    z-index: 999;
    float: right;
    position: absolute;
    background-color: white;
    color: red;
    right: 0px;
    top: 35px;
    width: 120px;
    height: 40px;
    border-radius: 0px 0px 15px 15px;
    line-height: 40px;
    text-align: center;
    box-shadow: 2px 2px black;

}

.apps {
    position: absolute;
    top:calc(20% + 120px);
    width: 552px;
    left: 50%;
    transform:  translate(-50%, 0);
    height: 120px;
    margin-top: 10px;
    display: flex;
    padding: 20px;
    /* gap: 40px; */
}

.apps2{
    position: absolute;
    width: auto;
    right: 140px;
    height: 100px;
    display: flex;
    gap: 20px;

}

.choice{
    background-color: transparent;
    width: 80px;
    height: 80px;
}
.choice:hover{
    cursor: pointer;
    /* background-color: #e7e0eb; */
}

.app-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    width: 40px;
    height: 40px;
    margin-top: -5px;
    margin-left: 16px;
    padding: 2px;
    border-radius: 50%;
    background-color: #fff;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}


.app-icon {
    width: 30px;
    height: auto;
}

.app-label {
    margin-top: 10px;
    font-size: 16px;
    color: #333;
    text-align: center;
}

</style>