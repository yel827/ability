<template>
  <div class="home">
    <div class="titleQ">日志列表</div>
    <div class="search">
      <!-- form表单 //form表单里面包含2个select/2个form-item/1个date-picker/-->
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        <!-- 租户名称 -->
        <span class="demonstration">租户名称</span>
        <el-input v-model="values" placeholder="租户名称" class="right"></el-input>
        <!-- 日志等级 -->
        <span class="demonstration">日志等级</span>
        <el-select v-model="formInline.level" filterable placeholder="日志等级" class="right">
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          ></el-option>
        </el-select>
        <!-- 来源IP -->
        <el-form-item label="来源IP" class="right">
          <el-input v-model="formInline.name" placeholder="来源IP"></el-input>
        </el-form-item>
        <!-- 时间选择 -->
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
        <!-- 搜索 导出当前按钮 -->
        <el-form-item>
          <el-button type="primary" @click="onSubmit" class="right">搜索</el-button>
          <el-button type="primary" class="right">导出当前</el-button>
        </el-form-item>
      </el-form>
    </div>
    <div id="mainl" style="width:100%;height:200px;"></div>
    <div id="Detailedy">
      <i class="el-icon-pie-chart" id="bd"></i> 列表明细
    </div>
    <!-- table布局 -->
    <el-table
      :data="tableData.slice((currentPage-1)*pagesize,currentPage*pagesize)"
      style="width: 100%;"
      class="tabP"
    >
      <el-table-column prop="logID" label="日志ID" width="180"></el-table-column>
      <el-table-column prop="tenantName" label="租户名" width="180"></el-table-column>
      <el-table-column prop="level" label="日志等级"></el-table-column>
      <el-table-column prop="source" label="来源IP"></el-table-column>
      <el-table-column prop="responseTime" label="调用时长"></el-table-column>
      <el-table-column prop="logType" label="日志类型"></el-table-column>
      <el-table-column prop="msg" label="日志内容"></el-table-column>
      <!-- 造作column -->
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button type="text" @click="viewdetail(scope.$index, scope.row)">
            <i class="icon iconfont icon-chakan" style="font-size:18px; font-weight:bold;"></i>
          </el-button>
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
    <el-dialog
      title="提示"
      :visible.sync="dialogDetailsVisible"
      width="50%"
      style="text-align:left"
    >
      <ul class="detailBox">
        <li>
          <div class="bg_cyan">日志 ID</div>
          <div class="msgBox">{{detailForm.logID}}</div>
        </li>
        <li>
          <div class="bg_cyan">租户名称</div>
          <div class="msgBox">{{detailForm.tenantName}}</div>
        </li>
        <li>
          <div class="bg_cyan">日志等级</div>
          <div class="msgBox">{{detailForm.level}}</div>
        </li>
        <li>
          <div class="bg_cyan">来源IP</div>
          <div class="msgBox">{{detailForm.source}}</div>
        </li>
        <li>
          <div class="bg_cyan">调用时长</div>
          <div class="msgBox">{{detailForm.updateTime}}</div>
        </li>
        <li style="border-bottom: 1px solid #000;"> 
          <div class="bg_cyan" style=" height:80px; line-height:80px;">日志信息</div>
          <div class="msgBox" style=" height:80px; line-height:80px;">{{detailForm.msg}}</div>
        </li>
      </ul>
    </el-dialog>
  </div>
</template> 

 <script>
import echarts from "echarts";
export default {
  data() {
    return {
      currentPage: 1, //初始页
      pagesize: 6, //每页的数据,
      total:0,
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
        region: "",
        level: ""
      },
      detailForm: {},
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
      dialogDetailsVisible: false,
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
        // {
        //   createTime:"",  //创建时间
        //   logID: 11,  // 日志ID
        //   logType:'',  //日志类型
        //   level:"",  //日志等级
        //              //租户名 ????
        //   source: "http://192.168.0.1:8080",  //来源IP
        //   responseTime: 500 , //调用时间
        //   msg: "日志正常",    //日志内容
        // }
      ]
    };
  },

  methods: {
    dateTransfer(date) {
      var y = date.getFullYear();
      var m = date.getMonth() + 1;
      m = m < 10 ? "0" + m : m;
      var d = date.getDate();
      d = d < 10 ? "0" + d : d;
      var h = date.getHours();
      var minute = date.getMinutes();
      minute = minute < 10 ? "0" + minute : minute;
      return y + "-" + m + "-" + d + " " + "00:00:00";
    },
    onSubmit() {
      if (!(this.formInline.level || this.formInline.name || this.value2)) {
        return;
      }
      var formData = {};
      // this.$data

      if (this.formInline.level) {
        formData.level = this.formInline.level;
      }
      if (this.formInline.name) {
        formData.source = this.formInline.name;
      }
      console.log(this.value2, "this.value2");

      if (this.value2 != "" && this.value2 != undefined) {
        formData.startTime = this.dateTransfer(this.value2[0]);
        formData.endTime = this.dateTransfer(this.value2[1]);
      }

      this.$axios
        .post("/oms-basic/abilityLog!selectLog.json", formData)
        .then(res => {
          this.tableData = res.data.data;
        })
        .catch(err => {});
      //
    },
    viewdetail(index, row) {
      var that = this
      console.log(index, "index");
      that.dialogDetailsVisible = true;
      that.detailForm = row;
    },

    //--------------------
    getEcharts() {
      var dataAxis = [
        "点",
        "击",
        "柱",
        "子",
        "或",
        "者",
        "两",
        "指",
        "在",
        "触",
        "屏",
        "上",
        "滑",
        "动",
        "能",
        "够",
        "自",
        "动",
        "缩",
        "放"
      ];
      var data = [
        220,
        182,
        191,
        234,
        290,
        330,
        310,
        123,
        442,
        321,
        90,
        149,
        210,
        122,
        133,
        334,
        198,
        123,
        125,
        220
      ];
      var yMax = 500;
      var dataShadow = [];

      for (var i = 0; i < data.length; i++) {
        dataShadow.push(yMax);
      }
      echarts.init(document.getElementById("mainl")).setOption({
        title: {},
        grid: {
          x: 25,
          y: 45,
          x2: 5,
          y2: 20,
          borderWidth: 1
        },

        xAxis: {
          data: dataAxis,
          axisLabel: {
            inside: true,
            textStyle: {
              color: "#fff"
            }
          },
          axisTick: {
            show: false
          },
          axisLine: {
            show: false
          },
          z: 10
        },
        yAxis: {
          axisLine: {
            show: false
          },
          axisTick: {
            show: false
          },
          axisLabel: {
            textStyle: {
              color: "#999"
            }
          }
        },
        dataZoom: [
          {
            type: "inside"
          }
        ],
        series: [
          {
            // For shadow
            type: "bar",
            itemStyle: {
              normal: { color: "rgba(0,0,0,0.05)" }
            },
            barGap: "-100%",
            barCategoryGap: "40%",
            data: dataShadow,
            animation: false
          },
          {
            type: "bar",
            itemStyle: {
              normal: {
                color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                  { offset: 0, color: "#83bff6" },
                  { offset: 0.5, color: "#188df0" },
                  { offset: 1, color: "#188df0" }
                ])
              },
              emphasis: {
                color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                  { offset: 0, color: "#2378f7" },
                  { offset: 0.7, color: "#2378f7" },
                  { offset: 1, color: "#83bff6" }
                ])
              }
            },
            data: data
          }
        ]
      });
      var zoomSize = 6;
    },

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
    },
    getJournal() {
      this.$axios
        .post("/oms-basic/abilityLog!selectLog.json", {})
        .then(res => {
          console.log(res.data.data, "res.data.data");
          this.tableData = this.tableData.concat(res.data.data);
          // console.log(this.tableData,"this.tableData")
          this.total = res.data.count
        })
        .catch(function(error) {
          console.log(error);
        });
    }
  },

  mounted() {
    this.getEcharts();
    this.getJournal();
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
/deep/.el-dialog__title{
  display: inline-block;
  width:100%;
  text-align: center;
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
.detailBox {
  li {
    border-top: 1px solid #000;
    border-left: 1px solid #000;
    display: flex;
    align-items: center;
    justify-content: space-between;
    .bg_cyan {
      line-height: 40px;
      background-color: #f9fbfd;
      width: 100px;
      font: 14px/40px "";
      text-align: center;
    
      border-right: 1px solid #000;
    }
    .msgBox {
      flex: 1;
      line-height: 40px;
      text-indent: 30px !important;
      
      border-right: 1px solid #000;
    }
  }
}
</style>
