<template>
  <div class="home">
    <div class="titleQ">异常日志列表</div>
    <div class="search">
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        <span class="demonstration">租户名称</span>
        <el-select v-model="values" filterable placeholder="请选择" class="right">
          <el-option
            v-for="item in optionss"
            :key="item.values"
            :label="item.labels"
            :value="item.values"
          ></el-option>
        </el-select>

        <span class="demonstration">日志等级</span>
        <el-select v-model="value" filterable placeholder="请选择" class="right">
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          ></el-option>
        </el-select>

        <el-form-item label="来源IP" class="right">
          <el-input v-model="formInline.name" placeholder="来源IP"></el-input>
        </el-form-item>
        <span class="demonstration">时间选择</span>
        <el-date-picker
          v-model="value2"
          type="daterange"
          align="right"
          class="right"
          unlink-panels
          range-separator="至"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          :picker-options="pickerOptions"
        ></el-date-picker>
        <el-form-item>
          <el-button type="primary" @click="onSubmit" class="right">搜索</el-button>
          <el-button type="primary" class="right">导出当前</el-button>
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
          <el-button type="text" @click="editgsForm(scope.$index, scope.row)"><i class="icon iconfont icon-chakan" style="font-size:18px; font-weight:bold;"></i></el-button>
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
      currentPage: 1, //初始页
      pagesize: 10, //每页的数据
      pickerOptions: {
        shortcuts: [
          {
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
              picker.$emit("pick", [start, end]);
            }
          },
          {
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
              picker.$emit("pick", [start, end]);
            }
          },
          {
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
              picker.$emit("pick", [start, end]);
            }
          }
        ]
      },
      value1: "",
      value2: "",
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
      total:0,
      dialogAddgsVisible: false,
      dialogEditgsVisible: false,
      dialogEditgsVisible1: false,
      options: [
        {
          value: "选项1",
          label: "黄金糕"
        },
        {
          value: "选项2",
          label: "双皮奶"
        },
        {
          value: "选项3",
          label: "蚵仔煎"
        },
        {
          value: "选项4",
          label: "龙须面"
        },
        {
          value: "选项5",
          label: "北京烤鸭"
        }
      ],
      value: "",
      optionss: [
        {
          values: "选项1",
          labels: "黄金糕"
        },
        {
          values: "选项2",
          labels: "双皮奶"
        },
        {
          values: "选项3",
          labels: "蚵仔煎"
        },
        {
          values: "选项4",
          labels: "龙须面"
        },
        {
          values: "选项5",
          labels: "北京烤鸭"
        }
      ],
      values: "",

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
          address: "上海市普陀区18 弄"
        },{
          id: "2016-05-02",
          name: "王2虎",
          address: "上海市普江路 1518 弄"
        },{
          id: "2016-05-02",
          name: "王2虎",
          address: "上海市普陀江路 1518 弄"
        }
        
      ],
      tableLabel: [
        { label: "日志ID", prop: "id" },
        { label: "租户名", prop: "name" },
        { label: "日志等级", prop: "address" },
        { label: "来源IP", prop: "address" },
        { label: "调用时间", prop: "address" },
        { label: "日志类型", prop: "address" },
        { label: "日志内容", prop: "address" }
      ]
    };
  },
  methods: {
    //////
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

    editgsForm(val) {
      this.$router.push("/LogDetails");
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
  },
  mounted() {
    this.getEcharts();
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
.right {
  margin-right: 24px;
}
#Detailedy {
  width: 100%;
  height: 50px;
  border-bottom: 1px solid #ccc;
  border-top: 1px solid #ccc;
  line-height: 50px;
  padding-left: 10px;

  box-sizing: border-box;
}
#bd {
  color: #3584f3;
  background: #d5e6ff;
  font-weight: bold;
  border-radius: 50%;
}
</style>
