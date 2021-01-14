<template>
  <div>
    <h1>商品分类管理</h1>

    <el-button @click="addFormShowFlag=true" type="primary" size="mini"  icon="el-icon-plus">增加</el-button>
    <el-tree
      :data="data"
      :props="defaultProps"
      accordion
      @node-click="handleNodeClick"
    >

      <span class="custom-tree-node" slot-scope="{ node, data }">
        <span>{{ node.label }}</span>
        <span>

    <el-button @click="updateFormShowFlag=true"  type="text" icon="el-icon-edit">修改</el-button>
      <el-button @click="deleteType()" type="text" icon="el-icon-delete">删除</el-button>
        </span>
      </span>
    </el-tree>



    <el-dialog
      title="增加类型"
      :visible.sync="addFormShowFlag">
      <el-form ref="addForm"  :model="form" style="width: 50%" label-width="80px">
        <el-form-item label="类型名称" prop="name1">
          <el-input v-model="form.name1"></el-input>
        </el-form-item>

        <el-form-item label="上级名称" prop="parentName">
          <el-input v-model="form.label"></el-input>
        </el-form-item>

        <el-form-item label="pid" prop="pid">
          <el-input v-model="form.pid"></el-input>
        </el-form-item>
        <el-button type="primary" @click="add" >新增</el-button>
      </el-form>
    </el-dialog>





    <el-dialog
      title="修改类型"
    :visible.sync="updateFormShowFlag">
    <el-form ref="updateForm"  :model="updateForm" style="width: 50%" label-width="80px">

      <el-form-item label="类型名称" prop="name">
        <el-input v-model="updateForm.name"></el-input>
      </el-form-item>

      <el-form-item label="上级名称" prop="parentName">
        <el-input v-model="updateForm.label" ></el-input>
      </el-form-item>

      <el-form-item label="pid" prop="pid">
        <el-input v-model="updateForm.pid" autocomplete="off" ></el-input>
      </el-form-item>

      <el-form-item label="类型" prop="isDel">
        <el-radio-group v-model="form.isDel">
          <el-radio label="0">未删除</el-radio>
          <el-radio label="1">已删除</el-radio>
        </el-radio-group>
      </el-form-item>

      <el-form-item>
      <el-button type="primary" @click="update" >提交</el-button>
      </el-form-item>
    </el-form>
    </el-dialog>



  </div>
</template>

<script>
    export default {
        name: "Type",
        data(){
            return{
                data:[],
                ajaxData:[],
                jsonStr:"",
                defaultProps:{},
                addFormShowFlag:false,
                updateFormShowFlag:false,
                form:{
                    pid:''
                },
                updateForm:{
                  name:''
                },

            }
        }
        ,methods: {
            handleNodeClick(data) {
                var athis = this;
                console.log(data);
                athis.form = data;
                athis.form.pid = data.id;


                athis.updateForm = data;
                athis.updateForm.name = data.label;
            },
            chandleData: function () {
                for (let i = 0; i < this.ajaxData.length; i++) {
                    if (this.ajaxData[i].pid == 0) {
                        this.diguiNode(this.ajaxData[i]);
                        break;
                    }
                }
                this.data.push(JSON.parse(this.jsonStr));

            },
            diguiNode: function (node) {
                var bf = this.isParent(node);
                if (bf == true) {
                    this.jsonStr += '{"id":' + node.id + ',"label":"' + node.name + '","children":[';
                    let count = 0
                    for (let i = 0; i < this.ajaxData.length; i++) {
                        if (node.id == this.ajaxData[i].pid) {
                            count++;
                            this.diguiNode(this.ajaxData[i]);
                            this.jsonStr += ",";
                        }
                    }
                    if (count != 0) {
                        this.jsonStr = this.jsonStr.substr(0, this.jsonStr.length - 1);
                    }
                    this.jsonStr += ']}';
                } else {
                    this.jsonStr += '{"id":' + node.id + ',"label":"' + node.name + '"}';
                }
            }
            , isParent: function (node) {
                for (let i = 0; i < this.ajaxData.length; i++) {
                    if (node.id == this.ajaxData[i].pid) {
                        return true;
                    }
                }
                return false;
            }, add: function () {
                var athis = this;
                athis.$ajax.post("http://192.168.1.178:8082/api/type/add", athis.$qs.stringify(athis.form)).then(function (rs) {
                }).catch(err >= console.log('增加失败'));
            },
            update:function () {
                var athis = this;
                athis.$ajax.post("http://192.168.1.178:8082/api/type/update", athis.$qs.stringify(athis.updateForm)).then(function (rs) {

                }).catch(function (err) {
                    console.log('修改失败');
                });
            }
        },
             created: function () {

                this.$ajax.get("http://192.168.1.178:8082/api/type/getData").then(res => {
                    this.ajaxData = res.data.data;
                    console.log(this.ajaxData);
                    this.chandleData();
                }).catch(err => console.log(err));
            }

    }
</script>

<style scoped>

</style>
