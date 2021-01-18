<template>
    <div>




    <el-button type="primary" icon="el-icon-plus"  @click="showAddFrom">增加</el-button>

    <el-table
      :data="attrData"
      style="width:100%"
      max-height="350"
      @row-click="getDetails">

      <el-table-column
        prop="id"
        label="序号"
        width="120">
      </el-table-column>

      <el-table-column
        prop="name"
        label="名称"
        width="180">
      </el-table-column>

      <el-table-column
      prop="nameCH"
      label="中文名称"
      width="200">
    </el-table-column>

      <el-table-column
        prop="typeName"
        label="分类"
        width="250">
      </el-table-column>


      <el-table-column
        prop="type"
        label="属性类型"
        width="200"
        :formatter="fmt">
      </el-table-column>


    <!--  <el-table-column
        prop="createDate"
        label="创建日期"
        width="150">
      </el-table-column>

      <el-table-column
        prop="updateDate"
        label="修改日期"
        width="170">
      </el-table-column>


      <el-table-column
        prop="author"
        label="操作人"
        width="150">
      </el-table-column>-->

      <el-table-column
        label="操作"
        width="220">
        <template slot-scope="scope">
          <el-button @click=" updateFormFlag=true" type="text" size="small">修改</el-button>
          <el-button v-if="scope.row.type!=3" @click="ShowValueTableDiv(scope.row)" type="text" size="small">属性值维护</el-button>
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






      <el-dialog title="商品属性信息录入" :visible.sync="updateFormFlag">

        <el-form ref="form" :model="updateForm" label-width="80px">

          <el-form-item label="分类">
            <el-select v-model="updateForm.typeId" placeholder="请选择分类">
              <el-option label="请选择" :value="-1" ></el-option>
              <el-option v-for="item in types" :key="item.id" :label="item.name" :value="item.id"></el-option>
            </el-select>
          </el-form-item>

          <el-form-item label="属性英文">
            <el-input v-model="updateForm.name"></el-input>
          </el-form-item>
          <el-form-item label="属性中文">
            <el-input v-model="updateForm.nameCH"></el-input>
          </el-form-item>

          <el-form-item label="属性类型">
            <el-radio-group v-model="updateForm.type">
              <el-radio :label="0">下拉框</el-radio>
              <el-radio :label="1">单选框</el-radio>
              <el-radio :label="2">复选框</el-radio>
              <el-radio :label="3">输入框</el-radio>
            </el-radio-group>
          </el-form-item>

          <el-form-item label="是否SKU">
            <el-switch v-model="updateForm.isSKUb"></el-switch>
          </el-form-item>
        </el-form>


        <div slot="footer" class="dialog-footer">
          <el-button >取 消</el-button>
          <el-button type="primary" @click="update" >确 定</el-button>
        </div>
      </el-dialog>







      <el-dialog title="商品属性信息录入" :visible.sync="dialogFormVisible">

        <el-form ref="form" :model="form" label-width="80px">

          <el-form-item label="分类">
            <el-select v-model="form.typeId" placeholder="请选择分类">
              <el-option label="请选择" :value="-1"></el-option>
              <el-option v-for="item in types" :key="item.id" :label="item.name" :value="item.id"></el-option>
            </el-select>
          </el-form-item>

          <el-form-item label="属性英文">
            <el-input v-model="form.name"></el-input>
          </el-form-item>
          <el-form-item label="属性中文">
            <el-input v-model="form.nameCH"></el-input>
          </el-form-item>

          <el-form-item label="属性类型">
            <el-radio-group v-model="form.type">
              <el-radio label="0">下拉框</el-radio>
              <el-radio label="1">单选框</el-radio>
              <el-radio label="2">复选框</el-radio>
              <el-radio label="3">输入框</el-radio>
            </el-radio-group>
          </el-form-item>

          <el-form-item label="是否SKU">
            <el-switch v-model="form.isSKUb"></el-switch>
          </el-form-item>
        </el-form>


        <div slot="footer" class="dialog-footer">
          <el-button >取 消</el-button>
          <el-button type="primary" @click="add" >确 定</el-button>
        </div>
      </el-dialog>













      <el-dialog :title=valueTitle  :visible.sync="ShowValueTable">

        <el-button type="text" icon="el-icon-plus" circle @click="showValueFrom">增加</el-button>

        <el-table :data="gridData"  v-if="!ShowValueFormTable">
          <el-table-column property="id" label="序号" width="150"></el-table-column>
          <el-table-column property="name" label="属性值" width="150"></el-table-column>
          <el-table-column property="nameCH" label="属性中文" width="200"></el-table-column>
          <el-table-column property="isDel" label="是否删除"></el-table-column>
          <el-table-column
            label="操作"
            width="170">
            <template slot-scope="scope">
              <el-button @click="() => updateFormFlag1=true" type="text" size="small">修改</el-button>
            </template>
          </el-table-column>
        </el-table>


        <el-pagination
          @current-change="handleCurrentChanges"
          @size-change="handleSizeChanges"
          :page-sizes="sizess"
          :page-size="sizez"
          layout="total, sizes, prev, pager, next, jumper"
          :total="counts">
        </el-pagination>


        <el-form :model="addvalueform" :rules="valuerules" ref="addvalueform"   label-width="80px" v-if="ShowValueFormTable">

          <el-form-item label="属性值" prop="name">
            <el-input v-model="addvalueform.name"></el-input>
          </el-form-item>

         <el-form-item label="中文名称"  prop="nameCH">
            <el-input v-model="addvalueform.nameCH"></el-input>
          </el-form-item>

          <el-form-item  v-if="condition"  label="attrId"  prop="attrId">
            <el-input  v-model="addvalueform.attrId"></el-input>
          </el-form-item>


          <el-form-item>
            <el-button type="primary" @click="addValue">添加</el-button>
            <el-button @click="ShowValueFormTable=false">取消</el-button>
          </el-form-item>
        </el-form>






      </el-dialog>





    </div>
</template>

<script>
    export default {
        name: "attr",
        data(){
            var checkname = (rule, value, callback) => {
                if (!value) {
                    return callback(new Error('属性名不能为空'));
                }
                if(/[a-zA-Z0-9_\u4e00-\u9fa5]+/.test(value)){
                    callback();
                }else{
                    callback(new Error('只能匹配中文，英文字母和数字并且至少有一个字符'));
                }
            };

            return{
                valueTitle:"",
                attrData:[],
                gridData:[],
                ShowValueTable:false,
                dialogFormVisible:false,
                form:{
                    typeId:-1
                },
                updateFormFlag:false,
                updateForm:{
                    typeId:"",
                    name:"",
                    nameCH:"",
                    type:"",
                    isSKUb:"",
                },
                types:[],

                count:0,
                sizes:[2,3,5,10],
                size:2,
                start:1,


                counts:0,
                sizess:[2,3,5,10],
                sizez:2,
                starts:1,

                ShowValueFormTable:false,
                addvalueform:{
                    name:"",
                    attrId:"",
                    nameCH:"",
                    attId:""
                },
                typeName:"",
                ajaxTypeData:[],
                valuerules:{
                    nameCH: [
                        { required: true, message: '请输入属性值的名称', trigger: 'blur' },
                        { max: 10, message: '长度不能超过 10 个字符', trigger: 'blur' },
                        { validator:checkname,trigger: 'blur' }
                    ],
                    name: [
                        { required: true, message: '请输入属性值', trigger: 'change' }
                    ]},

            }

        },
        methods:{

            formaterTypeData:function(){
                this.$ajax.get("http://localhost:8082/api/type/getData").then(res=>{
                    // [{id:1,"name":"",pid:2},{}]
                    this.ajaxTypeData=res.data.data;
                    this.getChildrenType();
                    for (let i = 0; i <this.types.length ; i++) {
                        this.typeName=""; // 全局变量   临时存 层级名称
                        this.chandleName(this.types[i]);
                        this.types[i].name=this.typeName.split("/").reverse().join("/").slice(0, this.typeName.length-1);
                        console.log(this.types[i].name);
                    }
                })
            },
            chandleName:function(node){
                if(node.pid!=0){ //临界值
                    this.typeName+="/"+node.name;
                    //上级节点
                    for (let i = 0; i <this.ajaxTypeData.length ; i++) {
                        if(node.pid==this.ajaxTypeData[i].id){
                            this.chandleName(this.ajaxTypeData[i]);
                            break;
                        }
                    }

                }else{
                    this.typeName+="/"+node.name;
                }
            },
            getChildrenType:function(){
                //遍历所有的节点数据
                for (let i = 0; i <this.ajaxTypeData.length ; i++) {
                    let  node=this.ajaxTypeData[i];
                    this.isChildrenNode(node);
                }
            },
            isChildrenNode:function(node){
                let rs=true; //标示
                for (let i = 0; i <this.ajaxTypeData.length ; i++) {
                    if(node.id==this.ajaxTypeData[i].pid){
                        rs=false;
                        break;
                    }
                }
                if(rs==true){
                    this.types.push(node);
                }
            },

            getDetails(row){
                //alert(row)

                var athis = this;
                athis.updateForm.id=row.id;
                console.log(athis.updateForm.id);
                athis.updateForm.typeId=row.typeId;
                console.log(row.typeId);
                console.log(athis.updateForm.typeId);
                athis.updateForm.name=row.name;
                athis.updateForm.nameCH=row.nameCH;
                athis.updateForm.type=row.type;
                athis.updateForm.isSKUb=row.isSKUb.checked;
            },
            update:function (rs) {
                console.log("ssss"+rs);
                this.updateForm.isSKU=this.updateForm.isSKUb?1:0;
                var a =this;
                this.$ajax.post("http://localhost:8082/api/attr/updateAttr ",this.$qs.stringify(this.updateBrandForm)).then(res=>{
                    this.updateFormFlag=false;
                    a.queryData(1);
                }).catch(err=>console.log(err))
            },
            add:function(){
                //处理一下
                this.form.isSKU=this.form.isSKUb?1:0;
                this.$ajax.post("http://localhost:8082/api/attr/addAttribute",this.$qs.stringify(this.form)).then(res=>{
                    this.dialogFormVisible=false;
                })
            },
            ShowValueTableDiv:function(row){
                this.ShowValueTable=true;
                this.valueTitle=row.nameCH+"的选项信息";
                this.addvalueform.attrId=row.id;

                this.queryValueTableData(row.id);

            },
            showAddFrom:function () {
                this.dialogFormVisible=true;
            },
            handleSizeChange:function(size){ //跳转页面
                this.size=size;
                this.queryData(size);
            },
            handleCurrentChange:function(start){
                console.log(start);
                this.start = start;
                this.queryData(start);
            },
            deleteBrand:function (id) {
                this.$ajax.delete("http://localhost:8082/api/attr/delAttribute?id="+id).then(function () {
                     confirm("确定删除吗?");
                    history.go(0);
                }).catch(function () {
                })

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
                }).catch(err=>console.log(err));
            },
            fmt:function(a,b,c,d){
                if(c==0){
                    return "下拉框";
                }
                if(c==1){
                    return "单选框";
                }if(c==2){
                    return "复选框";
                }if(c==3){
                    return "输入框";
                }
            },
            /*  属性值 相关的  */
            showValueFrom:function () {

                this.ShowValueFormTable=true

                this.addvalueform.conditioin=false;
            },
            handleSizeChanges:function(sizez){ //跳转页面
                this.sizez=sizez;
                this.queryValueTableData(sizez);
            },
            handleCurrentChanges:function(starts){
                console.log(starts);
                this.starts = starts;
                this.queryValueTableData(starts);
            },
            queryValueTableData:function(id){
                var athis=this;
                athis.addvalueform.attrId;
                //alert()

                this.$ajax.get("http://192.168.1.178:8082/api/attrValue/queryAttrValue?aid="+athis.addvalueform.attrId+'&size='+this.sizez+'&start='+this.starts).then(res=>{
                    console.log(res);
                    this.gridData=res.data.data.data;
                    console.log(this.gridData);
                    this.counts=res.data.data.count;
                }).catch(err=>console.log(err));
            },
            addValue:function(){

                var athis = this;
             athis.addvalueform.attrId = athis.addvalueform.attId;
                athis.addvalueform.attId  = athis.updateForm.id;

                this.$refs["addvalueform"].validate((valid) => {

                    if (valid) {
                        //发起请求 添加数据
                        this.$ajax.post("http://localhost:8082/api/attrValue/addAttrValue", this.$qs.stringify(this.addvalueform)).then(res => {
                            this.$message.success("新增成功");
                            //关闭form表单
                            this.ShowValueFormTable = false;
                            //刷新table
                            this.queryValueTableData(this.addvalueform.attrId);
                        })

                    }else {
                            return false;
                        }
                });
              }
            },

        created:function() {
            this.queryData();

            this.queryValueTableData();

            this.formaterTypeData();
        }
    }
</script>

<style scoped>

</style>
