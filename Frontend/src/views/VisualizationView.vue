<template>
    <div class="box">
        <el-card shadow="hover" class="card">
            <router-link :to="{ name: 'resumesview' }" class="back">&lt;返回</router-link>
            <el-carousel indicator-position="outside" class="carousel" height="80vh" :autoplay="false" trigger="click">
                <el-carousel-item class="carousel-item">
                    <educationChart class="chartA" :edu_data="edu_data" v-if="data_receive" />
                </el-carousel-item>
                <el-carousel-item class="carousel-item">
                    <locationChart class="chartB" :loc_data="loc_data" v-if="data_receive" />
                </el-carousel-item>
                <el-carousel-item class="carousel-item">
                    <experienceChart class="chartC" :exp_data="exp_data" v-if="data_receive" />
                </el-carousel-item>
            </el-carousel>
        </el-card>
    </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { req1 } from '@/utils/request'
import educationChart from '@/components/admin/educationChart.vue'
import locationChart from '@/components/admin/locationChart.vue'
import experienceChart from '@/components/admin/experienceChart.vue'

const edu_data = ref(null),
    exp_data = ref(null),
    loc_data = ref(null)
// 解决 chart 在 onMounted 渲染时还没接收到数据
const data_receive = ref(false)

onMounted(() => {
    req1.get('/req1/statistics/get-statistics-info/')
        .then(res => {
            const { education_statistics, experience_statistics, location_statistics } = res.data
            edu_data.value = education_statistics
            exp_data.value = experience_statistics
            loc_data.value = location_statistics
            data_receive.value = true
        })
        .catch(() => {})
})
</script>

<style lang="less" scoped>
.card {
    width: 70%;
    margin: 11vh auto;

    .back {
        position: absolute;
        right: 16%;
        font-size: 1.6rem;
        text-decoration: none;
        z-index: 10;
    }
}
</style>
