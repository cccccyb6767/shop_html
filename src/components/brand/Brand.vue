<template>

  <div>

     品牌列表

    <div id="searchDiv">
      <el-form :inline="true" :model="searchForm" class="demo-form-inline">

        <el-form-item label="名称">
          <el-input v-model="searchForm.name" placeholder="名称"></el-input>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" @click="queryData(1)">查询</el-button>
          <el-button type="success" @click="addFormFlag=true">新增</el-button>
        </el-form-item>

      </el-form>
    </div>

    <div id="brandTable">

      <el-table
        :data="brandData"
        style="width: 100%"
        max-height="350">

        <el-table-column
          prop="id"
          label="序号"
          width="80">
        </el-table-column>

        <el-table-column
          prop="name"
          label="名称"
          width="150">
        </el-table-column>

        <el-table-column
          prop="bandE"
          label="首字母"
          width="80">
        </el-table-column>


        <el-table-column label="品牌LOGO" width="100">
          <template scope="scope">
            <img :src="scope.row.imgPath" width="80" height="80" class="photo"/>
          </template>
        </el-table-column>

        <el-table-column
          prop="createDate"
          label="创建日期"
          width="130">
        </el-table-column>

        <el-table-column
          prop="updateDate"
          label="修改日期"
          width="130">
        </el-table-column>


        <el-table-column
          prop="author"
          label="操作人"
          width="130">
        </el-table-column>

        <el-table-column
          label="操作"
          width="100">
          <template slot-scope="scope">
            <el-button @click="showUpdate(scope.row.id)" type="text" size="small">修改</el-button>
            <el-button @click="delBrand(scope.row.id)" type="text" size="small">删除</el-button>
          </template>
        </el-table-column>
       </el-table>

      <el-pagination
        @current-change="handleCurrentChange"
        @size-change="handleSizeChange"
        :page-sizes="sizes"
        :page-size="searchForm.size"
        layout="total, sizes, prev, pager, next, jumper"
        :total="count">
      </el-pagination>

    </div>



  </div>


</template>

<script>
    export default {
        name: "brand",
        data(){
      return{
          brandData:[],
          searchForm:{
              name:"",

          },
          count:0,
          sizes:[2,3,5,10],
          size:4,
          start:1
      }
        },methods:{
            queryData:function(){
              var athis = this;
              console.log(athis);
                //参数格式化
                var searchStr=this.$qs.stringify(this.searchForm);
                console.log(searchStr);
                this.$ajax.get("http://192.168.1.178:8082/api/brand/queryBrand?size="+athis.size+"&start="+athis.start+"&"+searchStr).then(res=>{
                    console.log(res);
                    this.brandData=res.data.data.data;
                    console.log( this.brandData);
                    this.count=res.data.data.count;
                }).catch(err=>console.log(err));;
            },
            handleSizeChange:function(size){ //跳转页面
                this.searchForm.size=size;
                this.queryData(size);
            },
            handleCurrentChange:function(start){
                console.log(start);
                this.searchForm.start = start;
                this.queryData(start);
            }
        },created:function() {
            this.queryData(1,4);
        }
    }
</script>

<style scoped>

</style>
