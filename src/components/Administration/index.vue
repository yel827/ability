<template>
  <div class="home">
    <div class="titleQ">授权管理</div>
    <div class="search">
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        <el-form-item label="授权码">
          <el-input v-model="formInline.name" placeholder="授权码"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="onSubmit">搜索</el-button>
        </el-form-item>
      </el-form>
    </div>
    <el-table
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
            @close="closeDialogVisible"
            width="30%"
            center
          >
            <el-form :model="editForm" :rules="rules" ref="editForm">
              <el-form-item label="类别名称" :label-width="formLabelWidth">
                <el-input v-model="editForm.name" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="排序" :label-width="formLabelWidth">
                <el-input v-model.number="editForm.sort"></el-input>
              </el-form-item>
            </el-form>

            <div slot="footer" class="dialog-footer">
              <el-button @click="dialogEditgsVisible = false">取 消</el-button>
              <el-button type="primary" @click="saveEditForm('editForm')">确 定</el-button>
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
      currentPage: 1, //初始页
      pagesize: 10, //每页的数据
      total:0,
      formInline: {
        user: "",
        name: "",
        region: ""
      },
      addForm: {
        name: "",
        sort: 99
      },
      editForm: {
        name: "",
        sort: 99
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
      tableData: [
        
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
    formLabelWidth() {
      console.log("formLabelWidth");
    },
    closeDialogVisible() {
      console.log("closeDialogVisible");
    },
    currentPage1() {
      console.log("currentPage1");
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

    /**
     *点击编辑删除按钮，弹出编辑删除模态框
     * @param
     */
    editgsForm(val) {
      this.dialogEditgsVisible = true;
      (this.title = "编辑"), (this.title1 = "删除");
      this.editForm.id = val.id;
      this.editForm.name = val.name;
      this.editForm.sort = val.sort;
    },
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
  },
   mounted() {
    //调取能力值库 this.editForm.arrayAbility=res;

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
};
</script>

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
</style>
