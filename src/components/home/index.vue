<template>
  <div class="home">
    <div class="titleQ">租户管理</div>
    <!-- 头部结构 -->
    <div class="search">
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        <el-form-item label="租户ID">
          <el-input v-model="tableDataName" placeholder="租户ID"></el-input>
        </el-form-item>
        <el-form-item label="授权码">
          <el-input v-model="tableDataValue" placeholder="授权码"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="doFilter">搜索</el-button>
          <el-button @click="dialogVisible = true">+ 添加租户</el-button>
          <!--  -->
          <el-dialog title="提示" :visible.sync="dialogVisible" width="30%">
            <el-row>
              租户名称
              <el-input v-model="productName" style="width: 70%;margin: 10px 0 10px 0"></el-input>
            </el-row>
            <el-row>
              授权能力识别码
              <el-input v-model="value" style="width: 70%;margin: 10px 0 10px 0"></el-input>
            </el-row>
            <div slot="footer" class="dialog-footer">
              <el-button @click="dialogVisible = false">取 消</el-button>
              <el-button type="primary" @click="addData">确 定</el-button>
            </div>
          </el-dialog>
          <!--  -->
        </el-form-item>
      </el-form>
    </div>
    <!-- 表格布局结构   -->
    <el-table
      style="width: 100%;"
      class="tabP"
      :data="tableData.slice((currentPage-1)*pagesize,currentPage*pagesize)"
      fit
      border
      stripe
      tooltip-effect="dark"
      empty-text="暂无数据"
    >
      <template>
        <!-- v-for="(item, index) in tableLabel" -->
        <el-table-column show-overflow-tooltip prop="tenantID" label="租户ID"></el-table-column>
        <el-table-column show-overflow-tooltip prop="tenantName" label="住户名称"></el-table-column>
        <el-table-column show-overflow-tooltip prop="code" label="授权码"></el-table-column>
        <el-table-column show-overflow-tooltip prop="abilityIDs" label="授权能力识别码"></el-table-column>
        <el-table-column show-overflow-tooltip prop="abilityNames" label="授权能力">
          <!-- <template slot-scope="scope">
            <div v-for="(item,index) in scope.row.abilityNames.split(',')" :key="index">{{item}}</div>
          </template>-->
        </el-table-column>
        <el-table-column show-overflow-tooltip prop="createTime" label="创建时间"></el-table-column>
      </template>
      <el-table-column label="操作">
        <!-- 操作column -->
        <template slot-scope="scope">
          <!-- 编辑图标 -->
          <el-button type="text" @click="editgsForm(scope.$index, scope.row)">
            <i class="icon iconfont icon-bianji" style="font-size:18px; font-weight:bold;"></i>
          </el-button>
          <!-- 编辑弹窗 dialog-->
          <el-dialog
            class="headers"
            :title="title"
            :visible.sync="dialogEditgsVisible"
            width="30%"
            center
            @close="closeDialogVisible"
          >
            <el-form :model="editForm" :rules="rules" ref="editForm">
              <el-form-item label="租户ID" :label-width="formLabelWidth">
                <el-input readonly v-model="editForm.tenantID" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="租户名称" :label-width="formLabelWidth">
                <el-input v-model="editForm.tenantName" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="授权码" :label-width="formLabelWidth">
                <el-input readonly v-model="editForm.code" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="授权能力" :label-width="formLabelWidth">
                <el-select v-model="form.abilityId" placeholder="请选择" collapse-tags multiple>
                  <el-option
                    v-for="(o,j) in editForm.arrayAbility"
                    :key="j"
                    :label="o.abilityName"
                    :value="o.ID"
                  ></el-option>
                </el-select>
              </el-form-item>
            </el-form>

            <div slot="footer" class="dialog-footer">
              <el-button @click="dialogEditgsVisible = false">取 消</el-button>
              <el-button
                type="primary"
                @click="saveEditForm(editForm.arrayAbility,scope.row.tenantID,scope.row.tenantName)"
              >确 定</el-button>
            </div>
          </el-dialog>
          <!-- 设置 -->
          <el-button type="text" @click="edit">
            <i
              class="icon iconfont icon-icon-test"
              style="font-size:18px; font-weight:bold;margin-left:10px;"
            ></i>
          </el-button>
          <!-- 删除 -->
          <!-- 删除图标 -->
          <el-button type="text" @click="open(scope.row.tenantID)">
            <i class="icon iconfont icon-shanchu" style="font-size:18px; font-weight:bold ;"></i>
          </el-button>
          <!-- 删除弹窗 -->
          <el-dialog
            :title="title1"
            :visible.sync="dialogEditgsVisible1"
            width="30%"
            center
            @close="closeDialogVisible"
          >
            <span class="rmdata">确定要删除这条数据吗？</span>
            <div slot="footer" class="dialog-footer">
              <el-button @click="dialogEditgsVisible1 = false">取 消</el-button>
              <el-button type="primary" @click.native.prevent="deleteRow1(scope.row, tableData)">确 定</el-button>
            </div>
          </el-dialog>
        </template>
      </el-table-column>
    </el-table>

    <div class="block">
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page.sync="currentPage1"
        :page-size="100"
        layout="total, prev, pager, next"
        :total="total"
      ></el-pagination>
    </div>
  </div>
</template>

 <script>
export default {
  data() {
    return {
      dialogTableVisible: false,
      dialogFormVisible: false,
      value: "",
      search: "",
      dialogVisible: false,
      dialogVisibleb: false,
      visible: false,
      productName: "",
      productDescription: "",
      productNameb: "",
      productDescriptionb: "",
      _index: "",
      form: {
        name: "",
        abilityId: [],
        date1: "",
        date2: "",
        delivery: false,
        type: [],
        resource: "",
        desc: ""
      },
      formLabelWidth: "120px",
      //----------------------------------------
      currentPage: 1, //初始页
      pagesize: 10, //每页的数据
      userList: [],
      formInline: {
        user: "",
        name: "",
        region: ""
      },
      //点击编辑时的data
      addForm: {
        name: "",
        sort: 99
      },
      editForm: {
        name: "",
        sort: 99,
        arrayAbility: [],
        temArr:[]
      },
      title: "",
      title1: "",
      dialogAddgsVisible: false,
      dialogEditgsVisible: false,
      dialogEditgsVisible1: false,

      rules: {
        name: [
          { required: true, message: "请输入名称" },
          { min: 2, max: 10, message: "SSSSSSS", trigger: "blur" }
        ],
        sort: [{ type: "number", message: "11233552", trigger: "blur" }]
      },
      //搜索功能需要的data
      tableDataName: "", //租户ID
      tableDataValue: "", //授权码
      // tableDataEnd: [],
      totalItems: 0,
      filterTableData: [],
      flag: false,
      //列表数据
      total:0,
      tableData: [
        // {
        //   abilityIDs: "",
        //   abilityNames: "",
        //   code: "5PF8EWHn9hYd6OxBsV9Gsg==",
        //   createTime: "2019-10-15 16:21:42",
        //   tenantID: 8,
        //   tenantName: "中国联通"
        // },
        // {
        //   id: "2016-05-02",
        //   name: "王8虎",
        //   address: "上海市普陀区金沙江路 1515 弄"
        // }
      ],
      tableLabel: [
        { label: "租户ID", prop: "tenantID" },
        { label: "租户名称", prop: "tenantName" },
        { label: "授权码", prop: "code" },
        { label: "授权能力编号", prop: "abilityIDs" },
        { label: "授权能力", prop: "abilityNames" },
        { label: "创建时间", prop: "createTime" }
      ]
    };
  },
  methods: {
    //添加
    addData() {
      //显示弹窗
      var that = this;
      that.dialogVisible = false;
      //调取添加接口
      let header = {
        headers: {
          "Content-Type": "application/x-www-form-urlencoded"
        }
      };
      //1.
      var params = new URLSearchParams();
      params.append("tenantName", that.productName);
      params.append("abilityIDs", that.value);
      //2. that.$qs.stringify(params1)

      var params1 = {
        tenantName: that.productName,
        abilityIDs: that.value
      };
      that.$axios
        .post(
          "/oms-basic/tenant!addTenant.json",
          that.$qs.stringify(params1),
          header
        )
        //成功
        .then(res => {
          //返回的数据
          console.log("res777", res);
          //自己定义的空数组tableData
          // this.tableData.splice(0, this.tableData.length)
          var arrs = { tenantName: that.productName, abilityIDs: that.value };
          that.tableData.splice(that.tableData.length, 0, arrs);
          //发送ajax请求获取数据

          this.$axios
            .post("/oms-basic/tenant!selectTenantBy.json", {
              // tenantName: "110",
              // abilityIDs: "112"
            })
            .then(res => {
              // this.tableData = res.data.data;
              this.tableData = [].concat(res.data.data);
              // console.log(this.tableData, "this.tableData");
            });
        })
        //失败
        .catch(error => {
          console.log(error);
        });
    },

    handleSubmit() {
      console.log("0000000000");
      console.log(this.$data.form.name, this.$data.form.region, "=========");
    },
    //点击删除是触发函数
    open(index) {
      this.$confirm("此操作将删除该数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
        center: true
      })
        .then(() => {
          var that = this;
          console.log(index, "下标");
          var par = {
            tenantID: index
          };
          console.log(par);
          that.$axios
            .post(
              "/oms-basic/tenant!deleteTenant.json",
              that.$qs.stringify(par)
            )
            .then(res => {
              console.log(res);
              this.$axios
                .post("/oms-basic/tenant!selectTenantBy.json", {
                  // tenantName: "110",
                  // abilityIDs: "112"
                })
                .then(res => {
                  // this.tableData = res.data.data;
                  this.tableData = [].concat(res.data.data);
                  // console.log(this.tableData, "this.tableData");
                });
            });
        })
        .catch(() => {
          console.log("11111111");
          this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
    },
    formLabelWidth() {
      console.log("formLabelWidth");
    },
    closeDialogVisible() {
      console.log("closeDialogVisible");
    },
    currentPage1() {
      console.log("currentPage1");
    },
    // addTenant() {
    //   this.dialogTableVisible = true;
    //   this.dialogFormVisible = true;
    // },


    //搜索
    doFilter() {
      
      // if (this.tableDataName == "" && this.tableDataValue == "") {
      //   this.$message.warning("查询条件不能为空！");
      //   return;
      // }
      //this_.tableData = []; //tableData列表数据 //每次手动将数据置空,因为会出现多次点击搜索情况
      this.filtertableData = []; //过滤后的数据
      var IDcode = {
        tenantID:this.tableDataName,
        code:this.tableDataValue
      }
      this.$axios.post("/oms-basic/tenant!selectTenantBy.json",this.$qs.stringify(IDcode))
      .then(res=>{
        console.log(res.data.data)
        var subjectY = res.data.data
        // if(subjectY.tenantID === this.tableDataName.value &&
        //   subjectY.code == this.tableDataValue.value
        // ){
        //   this.filtertableData.push(subjectY);
        // }
        this.tableData=subjectY;
        this.total=res.data.count;

        console.log(this.filtertableData,"12")
        console.log(this.tableData,"ssss")
      })
      .catch(error=>{
        console.log(error)
      })
      //页面数据改变重新统计数据数量和当前页
      this.currentPage = 1;
      this.totalItems = this.filtertableData.length;
      //渲染表格,根据值
      this.currentChangePage(this.filtertableData);
      //页面初始化数据需要判断是否检索过
      this.flag = true;
    },











    currentChangePage(list) {
      let from = (this.currentPage - 1) * this.pageSize;
      let to = this.currentPage * this.pageSize;

      for (; from < to; from++) {
        if (list[from]) {
          this.tableData.push(list[from]);
        }
      }
    },
    // 初始页currentPage、初始每页数据数pagesize和数据data
    handleSizeChange: function(size) {
      this.pagesize = size;
      console.log(this.pagesize); //每页下拉显示数据
    },
    handleCurrentChange: function(currentPage) {
      this.currentPage = currentPage;
      // console.log(this.currentPage)  //点击第几页
    },
    deleteRow1(index) {
      this.tableData.forEach((item, index) => {});

      this.dialogEditgsVisible1 = false;
    },
    edit() {
      this.$router.push("/Administration");
    },
    /**
     *
     * @param
     */
    editgsForm(index, row) {
      this.dialogEditgsVisible = true;
      console.log(this.editForm.temArr);
      row.temArr=this.editForm.temArr;
      console.log(row, "row");
      // abilityIDs: "111,116,115,114,122,113,112,119"
      // abilityNames: "1人脸检测（照片中包含若干人脸）,2人脸特征值缓存更新,3人脸比对（两组512位双精度人脸特征值）,4人脸比对（一张单一人脸图片，一组512位双精度人脸特征值,5人脸信息识别（单一人脸图片）,6人脸比对（两张单一人脸图片）,7人脸照片转换为特征值（照片中只含一个人脸）,8人脸信息识别（512位双精度人脸特征值）"
      // arrayAbility: (8) [{…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}]
      // code: (...)
      // createTime: "2019-10-15 17:18:26"
      // tenantID: 10
      // tenantName: "电信"
      
      (this.title = "编辑"), (this.title1 = "删除");
      this.editForm = row;
      var arr1 = [],
        arr2 = [];
      console.log(row.abilityIDs, "row.abilityIDs"); //111,116,115,114,122,113,112,119 row.abilityIDs
      //arr1 = row.abilityIDs.split(","); //arr1是什么, row.abilityIDs是什么???
      console.log(arr1, "arr1"); //["111", "116", "115", "114", "122", "113", "112", "119"]
      arr2 = row.abilityNames.split(",");
      console.log(arr2, "arr2");
      var arr = [];

      // arr1.forEach((obj, index) => {
      //   //obj 是arr1中的每一项
      //   var o = {};
      //   o.ID = obj;
      //   o.abilityName = arr2[index];
      //   arr.push(o);
      //   console.log(o, "oooo");
      // });
      console.log(arr, "arr");
      //  [{value: "111", name: "人脸检测（照片中包含若干人脸）"},
      //   {value: "116", name: "人脸特征值缓存更新"},
      //   {value: "115", name: "人脸比对（两组512位双精度人脸特征值）"},
      //   {value: "114", name: "人脸比对（一张单一人脸图片，一组512位双精度人脸特征值"},
      //   {value: "122", name: "人脸信息识别（单一人脸图片）"},
      //   {value: "113", name: "人脸比对（两张单一人脸图片）"},
      //   {value: "112", name: "人脸照片转换为特征值（照片中只含一个人脸）"},
      //   {value: "119", name: "人脸信息识别（512位双精度人脸特征值）"}]
      this.form.abilityId = row.abilityIDs; //arr赋值给editForm.arrayAbility
      this.editForm.arrayAbility=this.editForm.temArr;
      console.log(this.editForm.temArr);
    },
   
    saveEditForm(aaa,bbb,name) {
      var that=this;
      var param=new URLSearchParams();
      param.append("tenantID",this.editForm.tenantID);
      param.append("tenantName",this.editForm.tenantName);
      param.append("abilityIDs",this.form.abilityId.join(","));
      console.log(this.form.abilityId);
      var params1={
        tenantID:this.editForm.tenantID,      
        abilityIDs:this.form.abilityId.join(",")
      }
      // var header={
      //   headers:{
      //     "Content-Type":"application/x-www-form-urlencoded"
      //   }
      // }
      this.$axios
        .post("/oms-basic/tenant!editTenant.json", param)
        .then(function(response) {
          console.log(response);
          if (response.data.code == 10000) {
            that.dialogEditgsVisible = false;
          }
        })
        .catch(function(error) {
          console.log(error);
        });

      // this.$refs[aaa].validate(valid => {
      //   console.log(this.$refs[aaa]);
      //   if (valid) {
      //     // this.$axios.put(`http://localhost:3000/admin/categories/${this.editForm.id}`,this.editForm).then( res =>{
      //     //   alert('更新成功');
      //     this.dialogEditgsVisible = false;
      //     this.init();
      //     console.log(valid);
      //     // })
      //   }
      // });
    },
    getData() {},
    getAbilitys(){
      let that=this;
      that.$axios.post('/oms-basic/ability!selectAbilityList.json').then((res)=>{
        that.editForm.temArr=res.data.data;
        console.log(that.editForm.temArr);
      }).catch(()=>{
        console.log("error")
      })
    }
  },
  mounted() {
    //调取能力值库 this.editForm.arrayAbility=res;
    this.getAbilitys();
    this.handleSubmit();

    //
    //发送ajax请求获取数据

    this.$axios
      .post("/oms-basic/tenant!selectTenantBy.json", {
        // tenantName: "110",
        // abilityIDs: "112"
      })
      .then(res => {
        // this.tableData = res.data.data;
        this.tableData = [].concat(res.data.data);
        this.total=res.data.count;
        // console.log(this.tableData, "this.tableData");
      });
  },
  created() {
    this.getData();
  }
};
</script>
<style>
.el-tooltip__popper {
  max-width: 200px !important;
}
</style>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.home {
  height: 870px;
  background: #fff;
  overflow: hidden;
  position: relative;
  .titleQ {
    width: 100%;
    height: 50px;
    border-bottom: 1px solid #ccc;
    line-height: 50px;
    text-indent: 1em;
    border-left: 8px solid #3f67e1;
    box-sizing: border-box;
  }

  .search {
    padding: 12px 0 0 12px;
  }
  .block {
    position: absolute;
    left: 30%;
    bottom: 5px;
  }
}
.rmdata {
  display: inline-block;
  width: 100%;
  font-size: 16px;
  text-align: center;
}
</style>
