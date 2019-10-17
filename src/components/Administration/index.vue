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
      :data="tableData.slice((currentPage-1)*pagesize,currentPage*pagesize)"
      style="width: 100%;"
      class="tabP"
    >
      <template v-for="(item, index) in tableLabel">
        <el-table-column :key="index" :prop="item.prop" :label="item.label" width></el-table-column>
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
        {
          id: "2016-05-02",
          name: "王2虎",
          address: "上海市普陀区金沙江路 1518 弄"
        },
        {
          id: "2016-05-04",
          name: "王3虎",
          address: "上海市普陀区金沙江路 1517 弄"
        },
        {
          id: "2016-05-03",
          name: "王4虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          id: "2016-05-03",
          name: "王5虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          id: "2016-05-03",
          name: "王6虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          id: "2016-05-03",
          name: "王7虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          id: "2016-05-03",
          name: "王8虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          id: "2016-05-03",
          name: "王9虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          id: "2016-05-03",
          name: "王10虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          id: "2019-05-03",
          name: "王9虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          id: "2019-05-03",
          name: "王9虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          id: "2019-05-03",
          name: "王9虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          id: "2019-05-03",
          name: "王9虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          id: "2019-05-03",
          name: "王9虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          id: "2019-05-03",
          name: "王9虎",
          address: "上海市普陀区金沙江路 1516 弄"
        }
      ],
      tableLabel: [
        { label: "租户ID", prop: "id" },
        { label: "租户名称", prop: "name" },
        { label: "授权码", prop: "address" },
        { label: "授权能力编号", prop: "address" },
        { label: "授权能力", prop: "address" },
        { label: "创建时间", prop: "address" }
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
    }
  }
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
