<template>
    <div class="search-form">

        <!-- 头部tab切换 -->
        <el-row type="flex" class="search-tab">
            <span v-for="(item, index) in tabs" :key="index"
            @click="handleSearchTab(item, index)"
            :class="{active: index === currentTab}">
                <i :class="item.icon"></i>{{item.name}}
            </span>
        </el-row> 

        <el-form class="search-form-content" ref="form" label-width="80px">
            <el-form-item label="出发城市">
                <!-- fetch-suggestions 返回输入建议的方法 -->
                <!-- select 点击展开列表触发 -->
                <!-- //:fetch-suggestions监听输入框输入，在实践中请求api -->
                <el-autocomplete
                :fetch-suggestions="queryDepartSearch"
                placeholder="请搜索出发城市"
                @select="handleDepartSelect"
                @blur=" handldDepartBlur"
                class="el-autocomplete"
                v-model="form.departCity"
                ></el-autocomplete>
            </el-form-item>
            <el-form-item label="到达城市">
                <el-autocomplete
                @blur=" handldDepartBlur"
                :fetch-suggestions="queryDestSearch"
                placeholder="请搜索到达城市"
                @select="handleDestSelect"
                class="el-autocomplete"
                v-model="form.destCode"
                ></el-autocomplete>
            </el-form-item>
            <el-form-item label="出发时间">
                <!-- change 用户确认选择日期时触发 -->
                <el-date-picker type="date" 
                placeholder="请选择日期" 
                style="width: 100%;"
                v-model="form.departData"
                @change="handleDate">
                </el-date-picker>
            </el-form-item>
            <el-form-item label="">
                <el-button style="width:100%;" 
                type="primary" 
                icon="el-icon-search"
                @click="handleSubmit">
                    搜索
                </el-button>
            </el-form-item>
            <div class="reverse">
                <span @click="handleReverse">换</span>
            </div>
        </el-form>  
      </div>
</template>

<script>
export default {
    data(){
        return {
            tabs: [
                {icon: "iconfont icondancheng", name: "单程"},
                {icon: "iconfont iconshuangxiang", name: "往返"}
            ],
            currentTab: 0,
             // 其他代码...
            form: {
                departCity: "", // 出发城市
                departCode: "", // 出发城市代码
                destCity: "",  // 到达城市
                destCode: "",  // 到达城市代码
                departDate: "", // 日期字符串
            },
            //出发城市列表
            departDate:[]
        }
    },
    methods: {
        // tab切换时触发
        handleSearchTab(item, index){
            
        },
        //出发城市失焦触发
        handldDepartBlur(){
            this.form.departCity =this.departDate[0].value;
            this.form.departCode =this.departDate[0].sort;
        },
        
        // 出发城市输入框获得焦点时触发
        // value 是选中的值，cb是回调函数，接收要展示的列表
        //cb可以接受数组把数据展示出来
        queryDepartSearch(value, cb){
            //没有值返回
            if(!value){
                return;
            }
            //console.log(value)
            this.$axios({
                 url: `/airs/city`,
                 params:{
                     name:value
                 }
            }).then(res =>{
                //console.log(res)
                const {data} =res.data
                console.log(data)
                const newData = data.map(v =>{
                    v.value =v.name.replace('市','')
                    // map返回的数组由return组成的
                    return v
                })
                this.departDate =newData;

                cb(data)
            })
            
            //根据value请求城市列表
           
            //将数据展示出来
        },

        // 目标城市输入框获得焦点时触发
        // value 是选中的值，cb是回调函数，接收要展示的列表
        queryDestSearch(value, cb){
            cb([
                {value: 1},
                {value: 2},
                {value: 3},
            ]);
        },
       
        // 出发城市下拉选择时触发
        //点击才能拿到数据
        handleDepartSelect(item) {
             this.form.departCity =item.value;
            this.form.departCode =item.sort;
        },

        // 目标城市下拉选择时触发
        handleDestSelect(item) {
           this.form.destCity = item.value;
           this.form.destCode = item.sort;

        },

        // 确认选择日期时触发
        handleDate(value){
            this.form.departDate = moment(value).format("YYYY-MM-DD");
        },

        // 触发和目标城市切换时触发
        handleReverse(){
            
        },

        // 提交表单是触发
        handleSubmit(){
           console.log(this.form)
        }
    },
    mounted() {
       
    }
}
</script>

<style scoped lang="less">
.search-form{
    border:1px #ddd solid;
    border-top:none;
    width:360px;
    height:350px;
    box-sizing: border-box;
}

.search-tab{
  span{
    display: block;
    flex:1;
    text-align: center;
    height:48px;
    line-height: 42px;
    box-sizing: border-box;
    border-top:3px #eee solid;
    background:#eee;
  }

  .active{
    border-top-color: orange;
    background:#fff;
  }

  i{
    margin-right:5px;
    font-size: 18px;

    &:first-child{
      font-size:16px;
    }
  }
}

.search-form-content{
  padding:15px 50px 15px 15px;
  position: relative;

  .el-autocomplete{
    width: 100%;
  }
}

.reverse{
  position:absolute;
  top: 35px;
  right:15px;

  &:after,&:before{
      display: block;
      content: "";
      position: absolute;
      left:-35px;
      width:25px;
      height:1px;
      background:#ccc;
  }

  &:after{
      top:0;
    }

    &:before{
      top:60px;
    }

  span{
    display: block;
    position:absolute;
    top: 20px;
    right:0;
    font-size:12px;
    background: #999;
    color:#fff;
    width:20px;
    height:20px;
    line-height: 18px;
    text-align: center;
    border-radius: 2px;
    cursor: pointer;

    &:after,&:before{
      display: block;
      content: "";
      position: absolute;
      left:10px;
      width:1px;
      height:20px;
      background:#ccc;
    }

    &:after{
      top:-20px;
    }

    &:before{
      top:20px;
    }
  }
}
</style>
