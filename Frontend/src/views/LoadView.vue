<template>
    <div class="box">
        <el-upload
            class="upload-demo"
            drag
            :action="Analysis_URL"
            :before-upload="beforeAvatarUpload"
            :auto-upload="false"
            ref="uploadRef"
            name="file"
            :on-success="handleUploadSuccess"
            :on-error="handleUploadError"
            :limit="1"
            :on-exceed="handleExceed"
            :show-file-list="showFileList"
            :headers="{ Authorization: `Bearer ${jwtToken}` }">
            <svg viewBox="0 0 1024 1024" width="50" height="50" color="grey">
                <path
                    fill="currentColor"
                    d="M544 864V672h128L512 480 352 672h128v192H320v-1.6c-5.376.32-10.496 1.6-16 1.6A240 240 0 0 1 64 624c0-123.136 93.12-223.488 212.608-237.248A239.808 239.808 0 0 1 512 192a239.872 239.872 0 0 1 235.456 194.752c119.488 13.76 212.48 114.112 212.48 237.248a240 240 0 0 1-240 240c-5.376 0-10.56-1.28-16-1.6v1.6H544z"></path>
            </svg>
            <div class="el-upload-text">拖入文件 <em>点击上传</em></div>
        </el-upload>
        <button @click="submitUpload" v-if="!isWaitingForResult" class="before-waiting-btn">开始解析</button>
        <button @click="submitUpload" v-if="isWaitingForResult" class="on-waiting-btn">正在分析</button>
        <el-dialog v-model="dialogTableVisible" title="" width="70%" top="2vh">
            <resumeData :resume_data="data" />
        </el-dialog>
    </div>
</template>

<script setup>
import { ref, shallowRef } from 'vue'
import { ElNotification, genFileId } from 'element-plus'
import resumeData from '@/components/common/resumeData.vue'
import { FILE_TYPE } from '@/constants/fileType'
import { userApi } from '@/api'
import { Analysis_URL } from '@/constants/url'

const uploadRef = ref()
const dialogTableVisible = ref(false)

const handleExceed = files => {
    uploadRef.value.clearFiles()
    const file = files[0]
    file.uid = genFileId()
    uploadRef.value.handleStart(file)
}

// 显示上传按钮还是进度条
const isWaitingForResult = ref(false)
// 选文件显示列表 但是上传不显示（不美观）
const showFileList = ref(true)
const jwtToken = ref('')

const beforeAvatarUpload = async rawFile => {
    isWaitingForResult.value = true
    if (!FILE_TYPE.includes(rawFile.type)) {
        ElNotification({
            title: '目前仅支持docx,pdf,txt格式的文件',
            type: 'warning'
        })
        isWaitingForResult.value = false
        return false
    } else if (rawFile.size > 5000 * 1024) {
        ElNotification({
            title: '文件过大',
            type: 'warning'
        })
        return false
    }

    try {
        const res = await userApi.getAuthorize()
        jwtToken.value = res.token
    } catch (err) {
        ElNotification({
            title: '服务器接口状态异常 | 请等待处理',
            type: 'error'
        })
        isWaitingForResult.value = false
        return false
    }
    showFileList.value = false
}

const data = shallowRef(null)

const handleUploadSuccess = res => {
    uploadRef.value.clearFiles()
    // 这里单独处理 因为单独用url而没用axios 所以没办法被拦截
    if (res.code === 400) {
        ElNotification({
            title: res.msg,
            type: 'warning'
        })
        showFileList.value = true
        isWaitingForResult.value = false
    } else {
        showFileList.value = true
        isWaitingForResult.value = false
        dialogTableVisible.value = true
        data.value = JSON.parse(res.res)
    }
}

const handleUploadError = error => {
    ElNotification({
        title: error,
        type: 'warning'
    })
}

const submitUpload = () => {
    uploadRef.value.submit()
}
</script>

<style lang="less" scoped>
.box {
    width: 37vw;
    margin: 0 auto;
    text-align: center;
}

.upload-demo {
    margin-top: 15vh;
}

.el-upload-text {
    height: 10vh;
    font-size: 2.4rem;
}

.before-waiting-btn,
.on-waiting-btn {
    position: relative;
    padding: 18px 50px;
    font-size: 28px;
    color: #ffffff;
    border-radius: 18px;
    margin-top: 4vh;
    border-color: transparent;
}
.before-waiting-btn {
    cursor: pointer;
    background: rgb(31, 169, 142);
    &:hover {
        z-index: 1;
        border-color: transparent;
        background: linear-gradient(90deg, #f9d923, #00a19d, #0c87b7, #cb49ff, #ff6666, #f9d923);
        background-size: 400%;
        box-shadow: 0 0 15px rgb(2, 4, 24);
        animation: glow 30s linear forwards;
    }
}

.on-waiting-btn {
    cursor: not-allowed;
    background: linear-gradient(90deg, #f9d923, #00a19d, #0c87b7, #cb49ff, #ff6666, #f9d923);
    background-size: 400%;
    animation: glow 30s linear forwards;
}

@keyframes glow {
    from {
        background-position: 0%;
    }

    to {
        background-position: 400%;
    }
}
</style>
