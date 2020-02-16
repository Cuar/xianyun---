<template>
    <section class="contianer">
        <el-row  type="flex" justify="space-between">

            <!-- 顶部过滤列表 -->
            <div class="flights-content">
                <!-- 过滤条件 -->
                <div>
                    
                </div>
                
                <!-- 航班头部布局 -->
                <FlightsListHead/>
                
                <!-- 航班信息 -->
                <FlightsItem
                v-for="(item,index) in flightsData.flights"
                :key='index'
                :data='item'
                />
            </div>

            <!-- 侧边栏 -->
            <div class="aside">
                <!-- 侧边栏组件 -->
            </div>
        </el-row>
    </section>
</template>

<script>
import FlightsListHead from "@/components/air/flightsListHead.vue"
import FlightsItem from "@/components/air/flightsItem.vue"
import moment from "moment";

export default {
      components: {
        FlightsListHead,FlightsItem 
    },
    data(){
        return {
            // 机票总数据 （有info, flights, total, options这些属性）
            flightsData: {}, // 航班总数据
            dataList: []     // 航班列表数据，用于循环flightsItem组件，单独出来是因为要分页
        }
    },
    mounted(){
        //请求机票数据
        this.$axios({
            url: "/airs",
            params:this.$route.query
        }).then(res =>{
            //总数据
            console.log(res)
            this.flightsData =res.data
        })
    }
}
</script>

<style scoped lang="less">
    .contianer{
        width:1000px;
        margin:20px auto;
    }

    .flights-content{
        width:745px;
        font-size:14px;
    }

    .aside{
        width:240px;
    } 
</style>