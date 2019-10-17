<template>
  <div class="mHome">
    <div class="titleQ">
      <el-tabs v-model="activeName" @tab-click="handleClick">
        <el-tab-pane label="用户管理" name="first">
          <div class="search">
            <el-form :inline="true" :model="formInline" class="demo-form-inline">
              <el-form-item label="主机名称" class="right">
                <el-input v-model="formInline.name" placeholder="主机名称"></el-input>
              </el-form-item>
              <el-form-item label="主机IP" class="right">
                <el-input v-model="formInline.name" placeholder="主机IP"></el-input>
              </el-form-item>
              <span class="demonstration">日志等级</span>
              <el-select v-model="values" filterable placeholder="请选择" class="right">
                <el-option
                  v-for="item in optionss"
                  :key="item.values"
                  :label="item.labels"
                  :value="item.values"
                ></el-option>
              </el-select>
              <el-form-item>
                <el-button type="primary" @click="onSubmit" class="right">搜索</el-button>
                <span class="icon_tu">
                  <span style="margin-right:20px;">
                    <i
                      class="el-icon-sunrise-1"
                      style="font-size:20px; margin-right:6px; color:#3584f3;"
                    ></i> 正常
                  </span>
                  <span>
                    <i
                      class="el-icon-sunrise-1"
                      style="font-size:20px; margin-right:6px; color:orange;"
                    ></i> 告警
                  </span>
                </span>
              </el-form-item>
            </el-form>
          </div>
          <el-table
            :data="tableData.slice((currentPage-1)*pagesize,currentPage*pagesize)"
            style="width: 100%;"
            class="tabP"
          >
            <el-table-column prop="date" label="ID" width="180"></el-table-column>
            <el-table-column prop="name" label="状态" width="180">
              <i class="el-icon-sunrise-1" style="font-size:20px; margin-right:6px; color:orange;"></i>
            </el-table-column>
            <el-table-column prop="name" label="主机名称"></el-table-column>
            <el-table-column prop="name" label="主机IP"></el-table-column>
            <el-table-column prop="name" label="操作系统"></el-table-column>
            <el-table-column prop="name" label="CPU利用率">
              <el-progress :percentage="percentage" :color="customColors"></el-progress>
            </el-table-column>
            <el-table-column prop="name" label="内存利用率">
              <el-progress :percentage="percentage1" :color="customColor"></el-progress>
            </el-table-column>
            <el-table-column prop="date" label="更新时间"></el-table-column>

            <el-table-column label="操作">
              <template slot-scope="scope">
                <el-button type="text" @click="editgsForm(scope.$index, scope.row)">
                <i class="icon iconfont icon-chakan"  style="font-size:18px; font-weight:bold;"></i>
                </el-button>
                <el-dialog
                  class="headers"
                  :title="title"
                  :visible.sync="dialogEditgsVisible"
                  width="50%"
                  center
                  @close="closeDialogVisible"
                >
                  <div
                    style="width:100%;
                         height:600px;
                         "
                  >
                    <div class="information">
                      <div>
                        <p>服务器信息</p>
                        <p>
                          <span>主机名称:主机名称abc</span>
                          <span>主机IP:111.111.111.11</span>
                          <span>操作系统:CentOs Linux release 7.2.1511 (Core)</span>
                        </p>
                        <p>
                          <span>CPU 18核</span>
                          <span style="margin-left:100px;">内存：32G</span>
                        </p>
                      </div>
                      <div class="myCPU">
                        <div>
                          <p>CPU状态监控</p>
                          <p style="text-align:center;">CPU指表</p>
                          <ul class="list">
                            <li>CPU主频·········2.40</li>
                            <li>CPU个数·········1个</li>
                            <li>CPU内核·········16个</li>
                          </ul>
                          <div class="el-progress-circle" style="height: 126px; width: 126px;">
                            <svg class="svgTick" viewBox="-50 -50 150 150">
                              <path
                                d="
                                  M 50 50
                                  m 0 -47
                                  a 47 47 0 1 1 0 94
                                  a 47 47 0 1 1 0 -94
                                  "
                                stroke="#e5e9f2"
                                stroke-width="4.8"
                                fill="none"
                                class="el-progress-circle__track"
                                style="stroke-dasharray: 295.31px, 295.31px; stroke-dashoffset: 0px;"
                              />
                              <path
                                d="
                                  M 50 50
                                  m 0 -47
                                  a 47 47 0 1 1 0 94
                                  a 47 47 0 1 1 0 -94
                                  "
                                stroke="#20a0ff"
                                fill="none"
                                stroke-linecap="round"
                                stroke-width="4.8"
                                class="el-progress-circle__path"
                                style="stroke-dasharray: 165.373px, 295.31px; stroke-dashoffset: 0px; transition: stroke-dasharray 0.6s ease 0s, stroke 0.6s ease 0s;"
                              />
                            </svg>
                            <p
                              style="
                            width:200px;
                            font-size:14px;
                            text-align:center;"
                            >CPU使用率</p>
                          </div>
                        </div>
                        <div>
                          <div
                            id="mainl"
                            style="width:500px; border:1px solid #ccc; margin-top:20px; height:200px;"
                          ></div>
                        </div>
                      </div>
                      <div class="myCPU">
                        <div>
                          <p>内存态监控</p>
                          <p style="text-align:center;">内存指表</p>
                          <ul class="list">
                            <li>内存容量·········32G</li>
                          </ul>
                          <div class="el-progress-circle" style="height: 126px; width: 126px;">
                            <svg class="svgTick" viewBox="-50 -50 150 150">
                              <path
                                d="
                                  M 50 50
                                  m 0 -47
                                  a 47 47 0 1 1 0 94
                                  a 47 47 0 1 1 0 -94
                                  "
                                stroke="#e5e9f2"
                                stroke-width="4.8"
                                fill="none"
                                class="el-progress-circle__track"
                                style="stroke-dasharray: 295.31px, 295.31px; stroke-dashoffset: 0px;"
                              />
                              <path
                                d="
                                  M 50 50
                                  m 0 -47
                                  a 47 47 0 1 1 0 94
                                  a 47 47 0 1 1 0 -94
                                  "
                                stroke="#20a0ff"
                                fill="none"
                                stroke-linecap="round"
                                stroke-width="4.8"
                                class="el-progress-circle__path"
                                style="stroke-dasharray: 165.373px, 295.31px; stroke-dashoffset: 0px; transition: stroke-dasharray 0.6s ease 0s, stroke 0.6s ease 0s;"
                              />
                            </svg>
                            <p
                              style="
                            width:200px;
                            font-size:14px;
                            text-align:center;"
                            >内存使用率</p>
                          </div>
                        </div>
                        <div>
                          <div
                            id="mainl"
                            style="width:500px; border:1px solid #ccc; margin-top:20px; height:200px;"
                          ></div>
                        </div>
                      </div>
                    </div>
                  </div>
                </el-dialog>
              </template>
            </el-table-column>
          </el-table>
        </el-tab-pane>

        <el-tab-pane label="配置管理" name="second">
          <div class="search">
            <el-form :inline="true" :model="formInline" class="demo-form-inline">
              <el-form-item label="主机名称" class="right">
                <el-input v-model="formInline.name" placeholder="主机名称"></el-input>
              </el-form-item>
              <el-form-item label="主机IP" class="right">
                <el-input v-model="formInline.name" placeholder="主机IP"></el-input>
              </el-form-item>
              <span class="demonstration">日志等级</span>
              <el-select v-model="values" filterable placeholder="请选择" class="right">
                <el-option
                  v-for="item in optionss"
                  :key="item.values"
                  :label="item.labels"
                  :value="item.values"
                ></el-option>
              </el-select>
              <el-form-item>
                <el-button type="primary" @click="onSubmit" class="right">搜索</el-button>
              </el-form-item>
            </el-form>
          </div>
          <el-table
            :data="tableData.slice((currentPage-1)*pagesize,currentPage*pagesize)"
            style="width: 100%;"
            class="tabP"
          >
            <el-table-column prop="date" label="组件ID" width="180"></el-table-column>
            <el-table-column prop="name" label="组件名称" width="180"></el-table-column>
            <el-table-column prop="name" label="组件数量"></el-table-column>
            <el-table-column prop="name" label="正常组件数量"></el-table-column>
            <el-table-column prop="name" label="告警组件数量"></el-table-column>
            <el-table-column prop="date" label="更新时间"></el-table-column>

            <el-table-column label="操作">
              <template slot-scope="scope">
                <el-button type="text" @click="editgsForm(scope.$index, scope.row)">
                  <i class="icon iconfont icon-chakan" style="font-size:18px; font-weight:bold;"></i>
                </el-button>
              </template>
            </el-table-column>
          </el-table>
        </el-tab-pane>
        <el-tab-pane label="角色管理" name="third">
          <div class="search">
            <el-form :inline="true" :model="formInline" class="demo-form-inline">
              <el-form-item label="主机名称" class="right">
                <el-input v-model="formInline.name" placeholder="主机名称"></el-input>
              </el-form-item>
              <el-form-item label="主机IP" class="right">
                <el-input v-model="formInline.name" placeholder="主机IP"></el-input>
              </el-form-item>
              <span class="demonstration">日志等级</span>
              <el-select v-model="values" filterable placeholder="请选择" class="right">
                <el-option
                  v-for="item in optionss"
                  :key="item.values"
                  :label="item.labels"
                  :value="item.values"
                ></el-option>
              </el-select>
              <el-form-item>
                <el-button type="primary" @click="onSubmit" class="right">搜索</el-button>
              </el-form-item>
            </el-form>
          </div>
          <el-table
            :data="tableData.slice((currentPage-1)*pagesize,currentPage*pagesize)"
            style="width: 100%;"
            class="tabP"
          >
            <el-table-column prop="date" label="能力ID" width="180"></el-table-column>
            <el-table-column prop="name" label="能力名称" width="180"></el-table-column>
            <el-table-column prop="name" label="组能力服务数量"></el-table-column>
            <el-table-column prop="name" label="正常状态服务数量"></el-table-column>
            <el-table-column prop="name" label="告警组件数量"></el-table-column>
            <el-table-column prop="date" label="更新时间"></el-table-column>

            <el-table-column label="操作">
              <template slot-scope="scope">
                <el-button type="text" @click="editgsForm(scope.$index, scope.row)">
                  <i class="icon iconfont icon-chakan" style="font-size:18px; font-weight:bold;"></i>
                </el-button>
              </template>
            </el-table-column>
          </el-table>
        </el-tab-pane>
      </el-tabs>
      <div class="block" id="vlok">
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
  </div>
</template>

 <script>
import echarts from "echarts";
export default {
  data() {
    return {
      activeName: "first",
      currentPage: 1, //初始页
      pagesize: 10, //每页的数据
      //进度条
      percentage: 20,
      percentage1: 35,
      customColor: "#409eff",
      customColors: [
        { color: "#f56c6c", percentage: 65 },
        { color: "#e6a23c", percentage: 70 },
        { color: "#5cb87a", percentage: 75 },
        { color: "#1989fa", percentage: 80 },
        { color: "#6f7ad3", percentage: 100 }
      ],
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
          date: "2016-05-02",
          name: "王2虎",
          address: "上海市普陀区金沙江路 1518 弄"
        },
        {
          date: "2016-05-04",
          name: "王3虎",
          address: "上海市普陀区金沙江路 1517 弄"
        },
        {
          date: "2016-05-03",
          name: "王4虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          date: "2016-05-03",
          name: "王5虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          date: "2016-05-03",
          name: "王6虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          date: "2016-05-03",
          name: "王7虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          date: "2016-05-03",
          name: "王8虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          date: "2016-05-03",
          name: "王9虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          date: "2016-05-03",
          name: "王10虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          date: "2019-05-03",
          name: "王9虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          date: "2019-05-03",
          name: "王9虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          date: "2019-05-03",
          name: "王9虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          date: "2019-05-03",
          name: "王9虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          date: "2019-05-03",
          name: "王9虎",
          address: "上海市普陀区金沙江路 1516 弄"
        },
        {
          date: "2019-05-03",
          name: "王9虎",
          address: "上海市普陀区金沙江路 1516 弄"
        }
      ]
    };
  },
  methods: {
    getEchartss() {
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
      myChart.on("click", function(params) {
        console.log(dataAxis[Math.max(params.dataIndex - zoomSize / 2, 0)]);
        myChart.dispatchAction({
          type: "dataZoom",
          startValue: dataAxis[Math.max(params.dataIndex - zoomSize / 2, 0)],
          endValue:
            dataAxis[Math.min(params.dataIndex + zoomSize / 2, data.length - 1)]
        });
      });
    },

    //进度条
    customColorMethod(percentage) {
      if (percentage < 30) {
        return "#909399";
      } else if (percentage < 70) {
        return "#e6a23c";
      } else {
        return "#67c23a";
      }
    },
    increase() {
      this.percentage += 10;
      if (this.percentage > 100) {
        this.percentage = 100;
      }
    },
    decrease() {
      this.percentage -= 10;
      if (this.percentage < 0) {
        this.percentage = 0;
      }
    },
    //
    handleClick(tab, event) {
      console.log(tab, event);
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

    editgsForm(val) {},
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
    open() {
      this.$alert(
        '<div style="width:500px; height:500px; background:red;"></div>',
        "主机监控详情",
        {
          dangerouslyUseHTMLString: true
        }
      );
    },
    //
    editgsForm(val) {
      this.dialogEditgsVisible = true;
      this.title = "主机监控详情";
      this.editForm.id = val.id;
      this.editForm.name = val.name;
      this.editForm.sort = val.sort;
    }

    //////
  },
  mounted() {
    this.getEchartss();
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.mHome {
  height: 870px;
  background: #fff;
  overflow: hidden;
  position: relative;
  /deep/ .el-tabs__nav.is-top {
    width: 100%;
    height: 50px;
    display: flex;
    flex-direction: rows;
    /deep/ .el-tabs__item.is-top {
      flex: 1;
      text-align: center;
    }
  }
}
.home {
  height: 870px;
  background: #fff;
  overflow: hidden;
}

.search {
  padding: 12px 0 0 12px;
}
#vlok {
  position: absolute;
  left: 30%;
  bottom: 5px;
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
.active {
  background: #3584f3;
  color: #fff;
}
// .block {
//   position: fixed;
//   left: 37%;
//   top: 915px;
// }
.icon_tu {
  display: inline-block;
  position: fixed;
  right: 4%;
}
.information {
  div:nth-child(1) {
    width: 100%;
    height: 100px;
    border-bottom: 1px solid #ccc;
    padding: 0 20px 20px 20px;
    box-sizing: border-box;
    background: #fff;
    p {
      line-height: 30px;
      span {
        margin-left: 22px;
      }
    }
  }
  div:nth-child(2) {
    width: 100%;
    height: 240px;
    border-bottom: 1px solid #ccc;
    padding: 0 20px 20px 20px;
    box-sizing: border-box;
    background: #fff;
  }
  div:nth-child(3) {
    width: 100%;
    height: 240px;
    border-bottom: 1px solid #ccc;
    padding: 0 20px 20px 20px;
    box-sizing: border-box;
    background: #fff;
  }
}
.myCPU {
  display: flex;
  flex-direction: row;
  div:nth-child(1) {
    width: 240px;
    height: 100%;
    border: none;
    .list {
      width: 100%;
      padding-left: 60px;
      box-sizing: border-box;
      li {
        line-height: 15px;
        font-size: 10px;
      }
    }
  }
  div:nth-child(2) {
    flex: 1;
    background: #fff;
  }
}
/deep/ .svgTick {
  margin-left: 40px;
  margin-top: -16px;
}
/deep/ .el-dialog__header {
  background: #3584f3;
  color: #fff;
  /deep/ .el-dialog__title {
    color: #fff;
  }
  /deep/ .el-dialog__close.el-icon.el-icon-close::before {
    color: #fff;
  }
}
</style>
