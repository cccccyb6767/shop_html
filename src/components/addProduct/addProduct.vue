<template>

  <div>
    <el-steps style="width:700px;margin-left:318px" :active="active" finish-status="success">
      <el-step title="基本信息"></el-step>
      <el-step title="商品属性"></el-step>
      <el-step title="商品图片"></el-step>
    </el-steps>

    <br/>
    <br/>
    <br/>
    <div v-if="active==0" align="center">
      <el-form  :model="productForm" :rules="rules" ref="productForm" label-width="100px" class="demo-ruleForm">
        <el-form-item style="margin-left:280px" label="商品名称" prop="name">
          <el-input style="width: 400px;margin-right:270px" v-model="productForm.name"></el-input>
        </el-form-item>

        <el-form-item style="margin-left:280px" label="标题" prop="title">
          <el-input style="width: 400px;margin-right:270px" v-model="productForm.title"></el-input>
        </el-form-item>

        <el-form-item style="margin-left:280px" label="品牌" prop="brandId">
          <el-select style="margin-right:270px" v-model="productForm.brandId" placeholder="请选择">
            <el-option v-for="b in brandDatas" :key="b.id" :label="b.name" :value="b.id"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item style="margin-left:280px" label="简介" prop="productdecs">
          <el-input style="width: 400px;margin-right:270px" type="textarea" v-model="productForm.productdecs"></el-input>
        </el-form-item>

        <el-form-item style="margin-left:280px" label="价格" prop="price">
          <el-input-number style="margin-right:270px" v-model="productForm.price" :precision="2" :step="0.1"></el-input-number>
        </el-form-item>

        <el-form-item style="margin-left:280px" label="库存" prop="stocks">
          <template>
            <el-input-number style="margin-right:270px" v-model="productForm.stocks" :step="10"></el-input-number>
          </template>
        </el-form-item>

        <el-form-item style="margin-left:280px" label="排序" prop="sortNum">
          <template>
            <el-input-number style="margin-right:270px" v-model="productForm.sortNum" :step="1"></el-input-number>
          </template>
        </el-form-item>

        <!-- 图片插件  -->
        <el-form-item style="margin-left:280px" label="主图" prop="imgPath">
          <el-upload
            class="upload-demo"
            action="http://192.168.1.178:8082/api/uploadFile/imgPath"
            :on-success="imgCallBack"
            :before-upload="beforeAvatarUpload"
            name="photo"
            list-type="picture"
            style="margin-right:270px">
            <img v-if="imageUrl" :src="imageUrl" class="avatar">
            <i v-else class="el-icon-plus avatar-uploader-icon"></i>
          </el-upload>
        </el-form-item>



<!--    -------------------    -->
<!--        <el-upload-->
<!--          action="http://192.168.1.178:8082/api/uploadFile/imgPath"-->
<!--          list-type="picture-card"-->
<!--          :auto-upload="false">-->
<!--          <i slot="default" class="el-icon-plus"></i>-->
<!--          <div slot="file" slot-scope="{file}">-->
<!--            <img-->
<!--              class="el-upload-list__item-thumbnail"-->
<!--              :src="file.url" alt=""-->
<!--            >-->
<!--            <span class="el-upload-list__item-actions">-->
<!--        <span-->
<!--          class="el-upload-list__item-preview"-->
<!--          @click="handlePictureCardPreview(file)"-->
<!--        >-->
<!--          <i class="el-icon-zoom-in"></i>-->
<!--        </span>-->
<!--        <span-->
<!--          v-if="!disabled"-->
<!--          class="el-upload-list__item-delete"-->
<!--          @click="handleDownload(file)"-->
<!--        >-->
<!--          <i class="el-icon-download"></i>-->
<!--        </span>-->
<!--        <span-->
<!--          v-if="!disabled"-->
<!--          class="el-upload-list__item-delete"-->
<!--          @click="handleRemove(file)"-->
<!--        >-->
<!--          <i class="el-icon-delete"></i>-->
<!--        </span>-->
<!--      </span>-->
<!--          </div>-->
<!--        </el-upload>-->
<!--        <el-dialog :visible.sync="dialogVisible">-->
<!--          <img width="100%" :src="imageUrl" alt="">-->
<!--        </el-dialog>-->




<!---------------------------->
        <el-form-item>
          <el-button>重置</el-button>
          <el-button type="primary" @click="next">下一步</el-button>
        </el-form-item>
      </el-form>
  </div>


    <div v-if="active==1">

      <el-form :model="productForm2" label-width="100px" class="demo-ruleForm">

        <el-form-item label="分类" prop="typeId">
          <!--  改变 获取属性数据  -->
          <el-select v-model="productForm2.typeId" placeholder="请选择" @change="getAttrData">
            <el-option v-for="b in types" :key="b.id" :label="b.name" :value="b.id"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item v-if="SKUData.length>0" label="商品规格" prop="name">

          <el-form-item v-for="a in  SKUData" :key="a.id" :label="a.nameCH">

            <!--  0 下拉框     1 单选框      2  复选框   3  输入框  -->
            <el-checkbox-group v-model="a.ckValues"   v-if="a.type==2" @change="skuChange">
              <el-checkbox v-for="b in a.values" :key="b.id" :label="b.nameCH" ></el-checkbox>
            </el-checkbox-group>

          </el-form-item>

        </el-form-item>


        <el-table
          v-if="tableShow"
          :data="tableSkuData"
          style="width: 100%">
          <!--   动态展示列头  sku属性中文名 -->
          <el-table-column v-for="c in cols" :key="c.id" :label="c.nameCH" :prop="c.name">
          </el-table-column>

          <el-table-column
            label="库存"
            width="180">

            <template slot-scope="scope">
              <el-input v-model="scope.row.storcks"/>
            </template>

          </el-table-column>
          <el-table-column
            label="价格"
            width="180">
            <template slot-scope="scope">
              <el-input v-model="scope.row.pricess"/>
            </template>
          </el-table-column>
        </el-table>




      <el-form-item v-if="attData.length>0" label="商品参数" prop="name">

        <el-form-item v-for="a in  attData" :key="a.id" :label="a.nameCH">

          <!--  0 下拉框     1 单选框      2  复选框   3  输入框  -->
          <el-input v-if="a.type==3" v-model="a.ckValues"></el-input>

          <el-select  v-if="a.type==0"  v-model="a.ckValues"   placeholder="请选择">
            <el-option v-for="b in a.values" :key="b.id"  :label="b.nameCH" :value="b.id"></el-option>
          </el-select>

          <el-radio-group  v-if="a.type==1"  v-model="a.ckValues" >
            <el-radio v-for="b in a.values" :key="b.id" :label="b.nameCH"></el-radio>
          </el-radio-group>

          <el-checkbox-group  v-if="a.type==2"  v-model="a.ckValues">
            <el-checkbox v-for="b in a.values" :key="b.id" :label="b.nameCH" name="type"></el-checkbox>
          </el-checkbox-group>

        </el-form-item>

      </el-form-item>

        <el-button type="primary" @click="active--">上一步</el-button>
        <el-button type="primary" @click="addProduct">添加</el-button>
      </el-form>



    </div>




    <div v-if="active==2">
      第三步信息
    </div>

</div>
</template>

<script>
    export default {
        name: "addProduct",
        data(){
            return{
                storcks:"", // 库存
                pricess:"", //  价格
                skuCK:[],
                cols:[],
                tableSkuData:[],//skutable的动态表头对应的表格数据
                tableShow:false, //sku的table是否显示
                ckValues:[],
                dialogVisible: false,
                disabled: false,
                active:0,
                productForm:{
                    name:"",
                    title:"",
                    brandId:"",
                    productdecs:"",
                    price:0,
                    stocks:0,
                    sortNum:0,
                    imgPath:""
                },
                rules:{},
                imageUrl:"",//图片显示用的
                brandDatas:[], //从接口拿数据
                productForm2:{},
                ajaxTypeData:[],
                typeName:"",
                types:[],
                attData:[],
                SKUData:[],
            }
        },methods:{
            addProduct:function(){
                //商品的类型id 添加到productform
                this.productForm.typeId=this.productForm2.typeId;
                console.log(this.productForm);
                //非sku的数据
                console.log(this.attData); //[{id: c,namech:系统,name:system,ckvaluwse:ios},{},{}]
                //sku数据
                console.log(this.tableSkuData);
                //声明后台接参的atrr
                let  atrrs=[];
                for (let i = 0; i <this.attData.length ; i++) {
                    let  attData={};
                    attData[this.attData[i].name]=this.attData[i].ckValues;
                    atrrs.push(attData);
                }
                this.productForm.attr=JSON.stringify(atrrs);
                this.productForm.sku=JSON.stringify(this.tableSkuData); //传参是string   怎么将js json 转为字符串
                //发起请求  保存数据
                this.$ajax.post("http://localhost:8082/api/com/addCommodity",this.$qs.stringify(this.productForm)).then(res=>{
                    this.$message.success("添加成功");
                })
            },
            calcDescartes:function(array) {
                console.log(array);
                if (array.length < 2) return array[0] || [];
                return [].reduce.call(array, function (col, set) {
                    var res = [];
                    col.forEach(function (c) {
                        set.forEach(function (s) {
                            var t = [].concat(Array.isArray(c) ? c : [c]);
                            t.push(s);
                            res.push(t);
                        })
                    });
                    return res;
                });
            },
            skuChange:function(){
                //笛卡尔积的参数
                let  kdej=[];
                //清空表格数据
                this.tableSkuData=[];
                //清空动态表头数据
                this.cols=[];
                // console.log(this.SKUData);
                //判断是否要生成笛卡尔积
                let flag=true;
                //遍历sku所有数据
                for (let i = 0; i <this.SKUData.length ; i++) {
                    //将sku属性 放入动态表头中
                    this.cols.push({"id":this.SKUData[i].id,"nameCH":this.SKUData[i].nameCH,"name":this.SKUData[i].name});
                    //将此属性选中的选项值放入笛卡尔积参数中
                    //得到当前sku选中的值  颜色  选中的值 [红色,绿色]    尺寸 [l,x]
                    kdej.push(this.SKUData[i].ckValues);
                    //判断此sku的复选框是否为选中
                    if(this.SKUData[i].ckValues.length==0){
                        flag=false; // 不能生成笛卡尔积
                        break;
                    }
                }
                if(flag==true){
                    //生成table的数据  [[],[],[]]   [{},{},{}]
                    let  datas=this.calcDescartes(kdej);
                    //遍历数据  [[红色,16g],[绿色,16g],[黑色，16g]]
                    for (let i = 0; i <datas.length ; i++) {
                        //遍历笛卡尔积 的每一项   [红色,16g]  cols:[{"id":1,"name": ,"nameCH"}]

                        let  valueAttr = datas[i];
                        let  jsonData={}; //笛卡尔积 转为json的对象
                        if(typeof valueAttr == "object"){
                        for (let j = 0; j <datas[i].length ; j++) {
                            //获取数据的key
                            let  key=this.cols[j].name;
                            jsonData[key]=datas[i][j]
                        }
                        }else{
                            let  key=this.cols[0].name;
                            jsonData[key]=valueAttr;
                        }
                        this.tableSkuData.push(jsonData);
                    }
                    console.log(this.tableSkuData);
                    console.log(datas);
                }
                this.tableShow=flag;
            },
            handleRemove(file) {
                console.log(file);
            },
            handlePictureCardPreview(file) {
                this.productForm.imageUrl = file.url;
                this.dialogVisible = true;
            },
            handleDownload(file) {
                console.log(file);
            },
            initBandData:function(){
                this.$ajax.get("http://localhost:8082/api/brand/queryBrandData").then(res=>{
                    this.brandDatas=res.data.data;
                    console.log(this.brandDatas);
                });
            },
            getAttrData:function(typeId){

                this.SKUData=[];
                this.attData=[];

                this.$ajax.get("http://localhost:8082/api/attr/queryDataByTypeId?typeId="+typeId).then(res=>{
                    let attrDatas=res.data.data;
                    //判断分类是否有数据   更新 参数和规格
                    if(attrDatas.length>0){
                        //初始化  attData      SKUData
                        for (let i = 0; i <attrDatas.length ; i++) {
                            //判断是否为sku属性
                            if(attrDatas[i].isSKU==0){

                                if(attrDatas[i].type!=3){
                                    attrDatas[i].ckValues=[];
                                    //发起请求 查询此属性对应的选项值
                                    this.$ajax.get("http://localhost:8082/api/attrValue/queryDataByAid?attrId="+attrDatas[i].id).then(res=>{
                                        attrDatas[i].values=res.data.data;
                                        console.log(res.data.data);
                                        this.attData.push(attrDatas[i]);

                                    })
                                }else{
                                    this.attData.push(attrDatas[i]);
                                }
                            }else{
                                if(attrDatas[i].type!=3){
                                    //发起请求 查询此属性对应的选项值
                                    this.$ajax.get("http://localhost:8082/api/attrValue/queryDataByAid?attrId="+attrDatas[i].id).then(res=>{
                                        attrDatas[i].values=res.data.data;
                                        console.log(res.data.data);
                                        attrDatas[i].ckValues=[];
                                        this.SKUData.push(attrDatas[i]);
                                    })
                                }else{
                                    attrDatas[i].ckValues=[];
                                    this.SKUData.push(attrDatas[i]);
                                }

                            }
                        }
                    }else{
                        this.SKUData=[];
                        this.attData=[];
                    }


                })
                console.log(this.attData);
            },   //初始化品牌数据

            //处理分类的下拉框   [{id:1,"name":"",pid:2},{}]
            // {"id":7,name:"分类/电子产品/手机"},
            formaterTypeData:function(){

                this.$ajax.get("http://localhost:8082/api/type/getData").then(res=>{

                    // [{id:1,"name":"",pid:2},{}]
                    this.ajaxTypeData=res.data.data;
                    //{"id":7,name:"分类/电子产品/手机"},
                    //先找到子节点的数据   this.types;
                    this.getChildrenType();
                    //遍历所有的子节点
                    for (let i = 0; i <this.types.length ; i++) {
                        this.typeName=""; // 全局变量   临时存 层级名称
                        //处理子节点的name属性
                        this.chandleName(this.types[i]);
                        //   a/b/c/f/d/e
                        //给name重新赋值
                        this.types[i].name=this.typeName.split("/").reverse().join("/").slice(0, this.typeName.length-1);
                    }

                })
            }, //给我一个节点  得到层级name
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
            //得到types的数据      遍历所有ajaxtypedata
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


            /*  步骤条  下一页  */
            next() {
                if(this.types.length==0){

                    this.formaterTypeData();
                }

                if (this.active++ > 1) this.active = 0;
            },
            imgCallBack(res, file) {
                //打断点 看怎么取返回值
                this.productForm.imgPath=res.data;

            },
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
        },
        created:function () {
            //初始化 品牌数据
            this.initBandData();
        }

    }
</script>

<style scoped>

</style>
