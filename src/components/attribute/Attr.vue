<template>
    <div>

  <div id="attrTable">

    <el-table
      :data="attrData"
      style="width: 100%"
      max-height="350">
<!--      @row-click="getDetails"-->

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
      prop="nameCH"
      label="中文名称"
      width="80">
    </el-table-column>


      <el-table-column
        prop="type"
        label="类型"
        width="80">
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
          <el-button @click="() => updateFormFlag=true" type="text" size="small">修改</el-button>
          <el-button @click="deleteBrand(scope.row.id)" type="text" size="small">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <el-pagination
      @current-change="handleCurrentChange"
      @size-change="handleSizeChange"
      :page-sizes="sizes"
      :page-size="size"
      layout="total, sizes, prev, pager, next, jumper"
      :total="count">
    </el-pagination>

  </div>

    </div>
</template>

<script>
    export default {
        name: "attr",
        data(){
            return{
                attrData:[],
                count:0,
                sizes:[2,3,5,10],
                size:4,
                start:1
            }
        },
        methods:{
            handleSizeChange:function(size){ //跳转页面
                this.size=size;
                this.queryData(size);
            },
            handleCurrentChange:function(start){
                console.log(start);
                this.start = start;
                this.queryData(start);
            },
            queryData:function(){
                var athis = this;
                console.log(athis);
                //参数格式化
                this.$ajax.get("http://192.168.1.178:8082/api/attr/queryAttribute?size="+athis.size+"&start="+athis.start).then(res=>{
                    console.log(res);
                    this.attrData=res.data.data.data;
                    console.log(this.attrData);
                    this.count=res.data.data.count;
                }).catch(err=>console.log(err));;
            }

        },
        created:function() {
            this.queryData(1,4);
        }
    }
</script>

<style scoped>

</style>
