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
          <el-button @click="addTenant">+ 添加租户</el-button>
          <!--  -->
          <el-dialog title="添加租户" :visible.sync="dialogFormVisible">
            <el-form :model="form">
              <el-form-item label="租户名称" :label-width="formLabelWidth">
                <el-input v-model="form.name" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="授权能力" :label-width="formLabelWidth">
                <el-select v-model="form.region" multiple placeholder="请选择授权能力">
                  <el-option label="语音能力" value="shanghai"></el-option>
                  <el-option label="视频流" value="beijing"></el-option>
                  <el-option label="分析能力" value="beijing111"></el-option>
                </el-select>
                <el-option
                  v-for="item in options"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
              <el-button @click="dialogFormVisible = false">取 消</el-button>
              <el-button type="primary" @click="handleSubmit ">确 定</el-button>
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
        <template slot-scope="scope">
          <el-button type="text" @click="editgsForm(scope.$index, scope.row)">
            <i class="icon iconfont icon-bianji" style="font-size:18px; font-weight:bold;"></i>
          </el-button>
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
                <el-input v-model="editForm.code" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="授权能力" :label-width="formLabelWidth">
                <el-select v-model="form.region" placeholder="请选择活动区域">
                  <el-option v-for="(o,j) in editForm.arrayAbility" :key="j" :label="o.name" :value="o.vlaue"></el-option>
                </el-select>
              </el-form-item>
            </el-form>

            <div slot="footer" class="dialog-footer">
              <el-button @click="dialogEditgsVisible = false">取 消</el-button>
              <el-button type="primary" @click="saveEditForm('editForm')">确 定</el-button>
            </div>
          </el-dialog>
<<<<<<< HEAD
          <el-button type="text" @click="edit">配置</el-button>
          <el-button type="text" @click="open(scope.row,scope.$index)">删除</el-button>
=======
          <el-button type="text" @click="edit" style="margin-left:10px;">
            <i class="icon iconfont icon-icon-test" style="font-size:18px; font-weight:bold;"></i>
          </el-button>
          <!--  -->
          
          <!--  -->
          <el-button type="text" @click="open(scope.$index)">
            <i class="icon iconfont icon-shanchu" style="color:orange; font-size:18px; font-weight:bold;"></i>
          </el-button>
>>>>>>> 24777a54e8eb9dae8eb05000d1411be808387570
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
        :total="1000"
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
      form: {
        name: "",
        region: "",
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
      //点击编辑是的data
      addForm: {
        name: "",
        sort: 99
      },
      editForm: {
        name: "",
        sort: 99,
        arrayAbility:''
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
      tableData: [
<<<<<<< HEAD
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
=======
        {
          abilityIDs: "1234",
          abilityNames: "5678",
          code: "5PF8EWHn9hYd6OxBsV9Gsg==",
          createTime: "2019-10-15 16:21:42",
          tenantID: 8,
          tenantName: "中国5联通"
        },
        {
          abilityIDs: "1234",
          abilityNames: "5678",
          code: "5PF8EWHn9hYd6OxBsV9Gsg==",
          createTime: "2019-10-15 16:21:42",
          tenantID: 8,
          tenantName: "中国7联通"
        },
        {
          abilityIDs: "1234",
          abilityNames: "5678",
          code: "5PF8EWHn9hYd6OxBsV9Gsg==",
          createTime: "2019-10-15 16:21:42",
          tenantID: 8,
          tenantName: "中国6联通"
        },
        {
          abilityIDs: "1234",
          abilityNames: "5678",
          code: "5PF8EWHn9hYd6OxBsV9Gsg==",
          createTime: "2019-10-15 16:21:42",
          tenantID: 8,
          tenantName: "中国5联通"
        },
        {
          abilityIDs: "1234",
          abilityNames: "5678",
          code: "5PF8EWHn9hYd6OxBsV9Gsg==",
          createTime: "2019-10-15 16:21:42",
          tenantID: 8,
          tenantName: "中国4联通"
        },
        {
          abilityIDs: "1234",
          abilityNames: "5678",
          code: "5PF8EWHn9hYd6OxBsV9Gsg==",
          createTime: "2019-10-15 16:21:42",
          tenantID: 8,
          tenantName: "中国3联通"
        },
        {
          abilityIDs: "1234",
          abilityNames: "5678",
          code: "5PF8EWHn9hYd6OxBsV9Gsg==",
          createTime: "2019-10-15 16:21:42",
          tenantID: 8,
          tenantName: "中国2联通"
        },
        {
          abilityIDs: "1234",
          abilityNames: "5678",
          code: "5PF8EWHn9hYd6OxBsV9Gsg==",
          createTime: "2019-10-15 16:21:42",
          tenantID: 8,
          tenantName: "中国1联通"
        }
>>>>>>> 24777a54e8eb9dae8eb05000d1411be808387570
      ],
      tableLabel: [
        { label: "租户ID", prop: "tenantID" },
        { label: "租户名称", prop: "tenantName" },
        { label: "授权码", prop: "code" },
        { label: "授权能力编号", prop: "abilityIDs" },
        { label: "授权能力", prop: "abilityNames" },
        { label: "创建时间", prop: "createTime" }
      ]
      // tableLabel: [
      //   { label: "租户ID", prop: "id" },
      //   { label: "租户名称", prop: "name" },
      //   { label: "授权码", prop: "address" },
      //   { label: "授权能力编号", prop: "address" },
      //   { label: "授权能力", prop: "address" },
      //   { label: "创建时间", prop: "address" }
      // ]
    };
  },
  methods: {
    handleSubmit() {
      console.log('0000000000')
      console.log(this.$data.form.name,this.$data.form.region,'=========')
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
          console.log(index);
          // this.tableData.splice(index, 1);

          this.$message({
            type: "success",
            message: "删除成功!"
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
    addTenant() {
      this.dialogTableVisible = true;
      this.dialogFormVisible = true;
    },
    doFilter() {
      if (this.tableDataName == "" || this.tableDataValue == "") {
        this.$message.warning("查询条件不能为空！");
        return;
      }
      // this.tableData = []; //tableData列表数据 //每次手动将数据置空,因为会出现多次点击搜索情况
      this.filtertableData = []; //过滤后的数据
      this.tableData.forEach((value, index) => {
<<<<<<< HEAD
        console.log(value, "value");
        if (value.tenantID && value.code) {
=======
        // console.log(value,'value')
        if (value.tenantID && value.tenantName) {
>>>>>>> 24777a54e8eb9dae8eb05000d1411be808387570
          console.log(this.tableDataName, this.tableDataValue, "----9");
          console.log(typeof value.tenantID.toString(), "value.tenantID"); //number
          if (
<<<<<<< HEAD
            value.tenantID.toString().indexOf(this.tableDataName) != -1 &&
            value.code.indexOf(this.tableDataValue) != -1
=======
            value.tenantID.indexOf(this.tableDataName) != -1 &&
            value.tenantName.indexOf(this.tableDataValue) != -1
>>>>>>> 24777a54e8eb9dae8eb05000d1411be808387570
          ) {
            this.filtertableData.push(value);
            console.log(this.filtertableData, "this.filtertableData");
          }
        }
        this.tableData = [].concat(this.filtertableData);
      });
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
     *点击编辑删除按钮，弹出编辑删除模态框
     * @param
     */
    editgsForm(index, row) {
      this.dialogEditgsVisible = true;
      (this.title = "编辑"), (this.title1 = "删除");
      this.editForm = row;
      var arr1=[],arr2=[];
      arr1=row.abilityIDs.split(',');
      arr2=row.abilityNames.split(',');
      var arr=[];
      
      arr1.forEach((obj,index)=>{
        var o={};
        o.value=obj;
        o.name=arr2[index];
        arr.push(o);
      })
      this.editForm.arrayAbility=arr;

    },

    saveEditForm(aaa) {
      this.$refs[aaa].validate(valid => {
        console.log(this.$refs[aaa]);
        if (valid) {
          // this.$axios.put(`http://localhost:3000/admin/categories/${this.editForm.id}`,this.editForm).then( res =>{
          //   alert('更新成功');
          this.dialogEditgsVisible = false;
          this.init();
          console.log(valid);
          // })
        }
      });
    },
    getData() {}
  },
  mounted() {
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
