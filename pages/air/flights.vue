<template>
    <section class="contianer">
        <el-row  type="flex" justify="space-between">

            <!-- 顶部过滤列表 -->
            <div class="flights-content">
                <!-- 过滤条件 -->
                <div>
                        <!-- 过滤条件 -->
                        <!-- 把值传给组件 -->
                <FlightsFilters :data='cacheFlightsData' @getData='getData' />
                </div>
                
                <!-- 航班头部布局 -->
                <FlightsListHead/>
                
                <!-- 航班信息 -->
                <FlightsItem
                v-for="(item,index) in dataList"
                :key='index'
                :data='item'
                />
                <!-- 分页 -->
                <!-- 使用一个分页组件 -->
                 <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="pageIndex"
      :page-sizes="[5, 10, 15, 20]"
      :page-size="pageSize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total">
    </el-pagination>
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
import FlightsFilters from "@/components/air/flightsFilters.vue"

export default {
      components: {
        FlightsListHead,FlightsItem ,FlightsFilters 
    },
    data(){
        return {
            // 机票总数据 （有info, flights, total, options这些属性）
            flightsData: {
                flights:[],
                info:{},
                options:{}
            },
            //备份数据 反正修改
            cacheFlightsData: {
                
                info:{},
                options:{}
            }, // 航班总数据
           // dataList: []   ,  // 航班列表数据，用于循环flightsItem组件，单独出来是因为要分页

            //当前页数
            pageIndex:1,
            //当前条数
            pageSize: 5,
            //总条数
            total:0,
        }
    },
    mounted(){
        //请求机票数据
        this.$axios({
            url: "/airs",
            params:this.$route.query
        }).then(res =>{
            //总数据
            
            this.flightsData =res.data
            //备份数据，拷贝一份出来
            this.cacheFlightsData ={...res.data}
            console.log(res.data)
            //修改总条数
            this.total =this.flightsData.total

        })
    },
    methods:{
            // 切换条数时触发
         handleSizeChange(index) {
        this.pageSize=index
      },
          // 切换页数时触发
      handleCurrentChange(index) {
        this.pageIndex =index
      },
      //获取过滤组件过滤后数据arr是数组
      getData(arr){
          this.flightsData.flights =arr
          //总条数
          this.total =arr.length;
      }
    },
    computed:{
        //计算属性函数内部引用实例（this）的属性一旦发生变化，函数就会重新执行返回新值
          dataList(){
            // 判断flightsData有没有值
            if(!this.flightsData.flights){
                // 没有值返回一个空数组
                return [];
            }

            // 第一页是0-5，第二页是5-10，第三页是10-15
            const arr = this.flightsData.flights.slice(
                (this.pageIndex - 1) * this.pageSize, 
                this.pageIndex * this.pageSize
            );
            return arr;
        }
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