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
        max-height="350"
        @row-click="getDetails">

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
            <el-button @click="() => updateFormFlag=true" type="text" size="small">修改</el-button>
            <el-button @click="deleteBrand(scope.row.id)" type="text" size="small">删除</el-button>
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



    <el-dialog
      title="增加品牌"
      :visible.sync="addFormFlag">
      <el-form ref="addForm"  :model="addBrandForm" style="width: 50%" label-width="80px">
        <el-form-item label="品牌名称" prop="name">
          <el-input v-model="addBrandForm.name"></el-input>
        </el-form-item>

        <el-form-item label="首字母" prop="bandE">
          <el-input v-model="addBrandForm.brandE"></el-input>
        </el-form-item>


        <el-form-item label="LOGO">
          <el-upload
            class="upload-demo"
            action="http://192.168.1.178:8082/api/uploadFile/imgPath"
            :on-success="imgCallBack"
            name="file"
            list-type="picture">
            <el-button size="small" type="primary">点击上传</el-button>
            <div slot="tip" class="el-upload__tip">不超过500kb</div>
          </el-upload>
        </el-form-item>

        <el-form-item label="描述信息" prop="bandDesc">
          <el-input type="textarea" v-model="addBrandForm.brandDesc"></el-input>
        </el-form-item>

        <el-form-item label="暗箱排序" prop="ord">
          <el-input v-model="addBrandForm.ord"></el-input>
        </el-form-item>

        <el-form-item label="是否展示" prop="isDel">
          <el-radio v-model="addBrandForm.isdel" label="0">展示</el-radio>
          <el-radio v-model="addBrandForm.isdel" label="1">不展示</el-radio>
        </el-form-item>


      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="addFormFlag = false">取 消</el-button>
        <el-button type="primary" @click="addForm">确 定</el-button>
      </div>
    </el-dialog>







    <el-dialog title="修改品牌信息" :visible.sync="updateFormFlag">

      <el-form :model="updateBrandForm" ref="updateBrandForm"  label-width="80px">

        <el-form-item label="id" prop="id">
          <el-input v-model="updateBrandForm.id" autocomplete="off" ></el-input>
        </el-form-item>

        <el-form-item label="品牌名称" prop="name">
          <el-input v-model="updateBrandForm.name" autocomplete="off" ></el-input>
        </el-form-item>


        <el-form-item label="首字母" prop="bandE">
          <el-input v-model="updateBrandForm.brandE"></el-input>
        </el-form-item>


        <el-form-item label="imgpath">
          <el-upload
            class="upload-demo"
            action="http://192.168.1.178:8082/api/uploadFile/imgPath"
            :on-success="imgCallBack"
            name="file"
            list-type="picture">
            <el-button size="small" type="primary">点击上传</el-button>
            <div slot="tip" class="el-upload__tip">不超过500kb</div>

          </el-upload>
        </el-form-item>

        <el-form-item label="描述信息" prop="bandDesc">
          <el-input type="textarea" v-model="updateBrandForm.brandDesc"></el-input>
        </el-form-item>

        <el-form-item label="暗箱排序" prop="ord">
          <el-input v-model="updateBrandForm.ord"></el-input>
        </el-form-item>



        <el-form-item label="操作人" prop="author">
          <el-input v-model="updateBrandForm.author"></el-input>
        </el-form-item>


      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="updateFormFlag = false">取 消</el-button>
        <el-button type="primary" @click="updateForm">确 定</el-button>
      </div>
    </el-dialog>

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
          updateFormFlag:false,
          updateBrandForm:{
              name:"",
              bandE:"",
              imgPath:"",
              bandDesc:"",
              ord:"",
              isDel:"0",
              author:""
          },
          addFormFlag:false,
          addBrandForm:{
              name:"",
              brandE:"",
              imgPath:"",
              brandDesc:"",
              ord:"",
              isDel:"0",
          },
          count:0,
          sizes:[2,3,5,10],
          size:4,
          start:1
      }
        },methods:{
            getDetails(row){
                //alert(row)
                var athis = this;
                athis.updateBrandForm.id=row.id;
                athis.updateBrandForm.name=row.name;
                athis.updateBrandForm.brandE=row.brandE;
                athis.updateBrandForm.brandDesc=row.brandDesc;
                athis.updateBrandForm.ord=row.ord;
                athis.updateBrandForm.author=row.author;
                athis.updateBrandForm.imgPath=row.imgpath;
                console.log(row)//此时就能拿到整行的信息
            },
            updateForm:function (rs) {
                console.log("ssss"+rs);
                var a =this;
                this.$ajax.post("http://localhost:8082/api/brand/updateBrand",this.$qs.stringify(this.updateBrandForm)).then(res=>{
                    this.updateFormFlag=false;
                    a.queryData(1);
                }).catch(err=>console.log(err))
            },
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
            addForm:function () {
                var athis=this;
                this.$ajax.post("http://localhost:8082/api/brand/addBrand",this.$qs.stringify(this.addBrandForm)).then(function () {
                    athis.addFormFlag = false;
                    history.go(0);
                }).catch(function () {

                })
            },imgCallBack:function(response, file, fileList){ //图片上传的回调函数
                // 赋值
                console.log(response);
                this.addBrandForm.imgPath=response.url;
            },
            deleteBrand:function (id) {
                this.$ajax.delete("http://localhost:8082/api/brand/delBrand?id="+id).then(function () {
                    history.go(0);
                }).catch(function () {
                })

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
