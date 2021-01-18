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


        <el-table-column
          prop="imgPath"
          label="品牌logo">
          <template slot-scope="scope">
            <img height="60px" :src="scope.row.imgPath"/>
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
          <el-input v-model="addBrandForm.bandE"></el-input>
        </el-form-item>


        <el-form-item label="LOGO">
          <el-upload
            class="upload-demo"
            action="http://192.168.1.178:8082/api/uploadFile/imgPath"
            :on-success="imgCallBack"
            :before-upload="beforeAvatarUpload"
            name="photo"
            list-type="picture">
            <img v-if="imageUrl" :src="imageUrl" class="avatar">
            <i v-else class="el-icon-plus avatar-uploader-icon"></i>
          </el-upload>
        </el-form-item>

        <el-form-item label="描述信息" prop="bandDesc">
          <el-input type="textarea" v-model="addBrandForm.bandDesc"></el-input>
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
          <el-input v-model="updateBrandForm.bandE"></el-input>
        </el-form-item>


        <el-form-item label="logo">
          <el-upload
            class="upload-demo"
            action="http://192.168.1.178:8082/api/uploadFile/imgPath"
            :on-success="imgCallBack"
            :before-upload="beforeAvatarUpload"
            name="photo"
            list-type="picture">
            <img v-if="imageUrl" :src="imageUrl" class="avatar">
            <i v-else class="el-icon-plus avatar-uploader-icon"></i>

          </el-upload>
        </el-form-item>

        <el-form-item label="描述信息" prop="bandDesc">
          <el-input type="textarea" v-model="updateBrandForm.bandDesc"></el-input>
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
          imageUrl:"",
          brandData:[],
          searchForm:{
              name:""
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
              bandE:"",
              imgPath:"",
              bandDesc:"",
              ord:"",
              isDel:"0",
          },
          size:2,
          start:1,
          count:0,
          sizes:[2,4,15]
      }
        },methods:{
            beforeAvatarUpload(file) {
                //限制类型    name  来限制
                const isJPG = file.type === 'image/jpeg';
                const isLt2M = file.size / 1024 / 1024 < 4;

                if (!isJPG) {
                    this.$message.error('上传头像图片只能是 JPG 格式!');
                }
                if (!isLt2M) {
                    this.$message.error('上传头像图片大小不能超过 2MB!');
                }
                return isJPG && isLt2M;
            },
            getDetails(row){
                //alert(row)
                var athis = this;
                athis.updateBrandForm.id=row.id;
                athis.updateBrandForm.name=row.name;
                athis.updateBrandForm.bandE=row.bandE;
                athis.updateBrandForm.bandDesc=row.bandDesc;
                athis.updateBrandForm.ord=row.ord;
                athis.updateBrandForm.author=row.author;
                athis.updateBrandForm.imgPath=row.imgPath;
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
                var searchStr=athis.$qs.stringify(athis.searchForm);
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
                this.addBrandForm.imgPath=response.data;
                this.updateBrandForm.imgPath=response.data;
            },
            deleteBrand:function (id) {
                this.$ajax.delete("http://localhost:8082/api/brand/delBrand?id="+id).then(function () {
                    history.go(0);
                }).catch(function () {
                })

            },
            handleSizeChange:function(size){ //跳转页面
                this.size=size;
                this.queryData(size);
            },
            handleCurrentChange:function(start){
                console.log(start);
                this.start = start;
                this.queryData(start);
            }
        },created:function() {
            this.queryData();
        }
    }
</script>

<style scoped>

</style>
