<template>
    <div class="box">
        <div class="content">
            <h1 class="topic">智能简历解析系统 V{{ envStore.version }}</h1>
            <div class="desc">基于自然语言处理技术对简历进行关键信息提取和岗位匹配</div>
            <div class="desc">整合后端技术为求职者和招聘者搭建沟通桥梁</div>
            <div class="btn-group">
                <button class="get-start" @click="getStart">开始体验</button>
                <button class="get-author" @click="getAuthor">联系我们</button>
            </div>
        </div>
    </div>
</template>

<script setup>
import { useRouter } from 'vue-router'
import { EnvStore } from '@/stores/EnvStore'
import { GITHUB_URL } from '@/constants/url'

const router = useRouter()
const getStart = () => {
    router.push({ name: 'loadview' })
}

const getAuthor = () => {
    window.open(GITHUB_URL)
}

const envStore = EnvStore()
</script>

<style lang="less" scoped>
.box {
    display: flex;
    justify-content: center;
}

.content {
    margin-top: 12vh;
    margin-bottom: 2vh;
}

.topic {
    font-size: 5.5rem;
    text-align: center;
    background-image: -webkit-linear-gradient(45deg, #43b375 25%, #3e5df7);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
}

.desc {
    text-align: center;
    font-size: 2.7rem;
    color: grey;
}

.btn-group {
    margin-top: 10vh;
}

.get-start {
    border: none;
    width: 130px;
    height: 53px;
    background-color: rgb(66, 184, 131);
    border-radius: 10px;
    color: white;
    font-size: 1.8rem;
    letter-spacing: 0.2rem;
    font-weight: bold;
    margin-left: 26%;
    cursor: pointer;

    &:hover {
        background-color: rgb(71, 200, 142);
        transition: 0.5s;
    }
}

.get-start svg {
    vertical-align: middle;
}

.get-author {
    border: 2px solid rgb(150, 150, 150);
    width: 130px;
    height: 53px;
    border-radius: 10px;
    background-color: rgb(232, 241, 247);
    margin-left: 10%;
    color: rgb(44, 44, 44);
    font-weight: bold;
    letter-spacing: 0.2rem;
    font-size: 1.8rem;
    cursor: pointer;

    &:hover {
        transition: 0.5s;
        background-color: rgb(76, 76, 76);
        color: rgb(232, 241, 247);
    }
}
</style>
