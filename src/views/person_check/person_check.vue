<template>
  <div v-height>
    <!-- 面包屑 -->
    <div class="mianbaoxie">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item>环境检测</el-breadcrumb-item>
        <el-breadcrumb-item>分析决策</el-breadcrumb-item>
        <el-breadcrumb-item class="link">个人检测统计</el-breadcrumb-item>
      </el-breadcrumb>
    </div>

     <!-- 人员选择 -->
    <div class="select_time">
      <span>
        <el-form>
          <el-form-item label="检测科室" class="time">
            <div class="block">
              <el-select v-model="search_name" multiple filterable allow-create default-first-option placeholder="请选择检测科室，可多选" style="width: 300px;">
                <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value">
                </el-option>
              </el-select>
            </div>
          </el-form-item>

        </el-form>
        <el-form style="margin-left: 20px;">
          <el-form-item label="人员选择" class="time">
            <div class="block">
              <el-select v-model="search_name" multiple filterable allow-create default-first-option placeholder="请选择人员，可多选" style="width: 300px;">
                <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value">
                </el-option>
              </el-select>
            </div>
          </el-form-item>
        </el-form>
        <el-form style="margin-left: 20px;">
          <el-form-item label="检测日期" class="time">
            <div class="block">
              <el-date-picker v-model="value2" type="daterange" align="right" unlink-panels range-separator="至" start-placeholder="开始日期" end-placeholder="结束日期" :picker-options="pickerOptions" style='width:350px;'>
              </el-date-picker>
              <el-button v-waves type="primary" style="width:100px;margin-top: -0.02rem;margin-left: 20px;" icon="el-icon-search" class="btn">搜索</el-button>
            </div>
          </el-form-item>
        </el-form>
      </span>
      <el-form style="margin-right: 20px;">
        <div class="block">
          <el-button v-waves style="width:60px;" type="danger" @click="exportFile" plain>导出</el-button>
        </div>
      </el-form>
    </div>
        <!-- 饼状图--列表 -->
     <div class='flex'> 
       <div class="right left">
        <div class="title">
          个人检测列表
        </div>
        <div class="table">
          <el-table ref="multipleTable" height='100%' border :data="tableData" tooltip-effect="dark" style="width: 100%" :cell-style="{padding:'15px 0'}" @selection-change="handleSelectionChange">
            <el-table-column label="序号" align="center" width="60">
              <template slot-scope="scope">
                {{scope.$index+1}}
              </template>
            </el-table-column>
            <el-table-column prop="name" label="科室名称" sortable>
            </el-table-column>
            <el-table-column prop="pact_num" label="检测项目量（个）" sortable>
            </el-table-column>
            <el-table-column label="项目量占比" prop="pact_per" sortable>
            </el-table-column>
          </el-table>
        </div>
        <div class="page">
          <el-pagination background layout="prev, pager, next" :total="1000">
          </el-pagination>
        </div>
      </div> 
      <div class="left">
        <div class="title">
          科室员工工作量比率
        </div>
        <!-- 统计图 -->
        <div class="rate">
          <div id="myChart" style="width: 100%;height:440px;"></div>
        </div>
      </div>
    </div> 
  </div>
</template>

<script>
import echarts from 'echarts'
export default {
  name: 'person_pact',
  data() {
    return {
      search_name: '',
      data_value: '',
      value2: '',
      tableData: [ ],
      multipleSelection: [],
      options: [
        {
          value: '选项1',
          label: '黄金糕'
        }, 
        {
          value: '选项2',
          label: '双皮奶'
        }, 
        {
          value: '选项3',
          label: '蚵仔煎'
        }, 
        {
          value: '选项4',
          label: '龙须面'
        }, 
        {
          value: '选项5',
          label: '北京烤鸭'
        }
      ],
      pickerOptions: {
        shortcuts: [{
          text: '最近一周',
          onClick(picker) {
            const end = new Date();
            const start = new Date();
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
            picker.$emit('pick', [start, end]);
          }
        }, {
          text: '最近一个月',
          onClick(picker) {
            const end = new Date();
            const start = new Date();
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
            picker.$emit('pick', [start, end]);
          }
        }, {
          text: '最近三个月',
          onClick(picker) {
            const end = new Date();
            const start = new Date();
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
            picker.$emit('pick', [start, end]);
          }
        }]
      },
    }
  },
  methods: {
    init() {
      // 绘制图表
      let option = {
        tooltip: {
          trigger: 'item',
          formatter: '{a} <br/>{b}: {c} ({d}%)'
        },
        legend: {
          orient: 'vertical',
          right: 10,
          data: ['金琪', '刘广玥', '纪华楠', '林嘉琪', '王营营', '秦延涛']
        },
        series: [
          {
            name: '报告差错率统计',
            type: 'pie',
            radius: ['30%', '70%'],
            avoidLabelOverlap: false,
            label: {
              show: false,
              position: 'center'
            },
            emphasis: {
              label: {
                show: true,
                fontSize: '15',
                fontWeight: 'bold'
              }
            },
            labelLine: {
              show: false
            },
            data: [
              { value: 45, name: '金琪' },
              { value: 310, name: '刘广玥' },
              { value: 45, name: '纪华楠' },
              { value: 310, name: '林嘉琪' },
              { value: 45, name: '王营营' },
              { value: 310, name: '秦延涛' },

            ],
            itemStyle: {
              emphasis: { //饼状图阴影。外发光
                shadowBlur: 10,
                shadowOffsetX: 0,
                shadowColor: 'rgba(0, 0, 0, 0.5)'
              },
              normal: {
                color: function (params) {
                  //自定义颜色
                  var colorList = [
                    '#53aae4', '#c36662', '#e99d3d', '#f5dc4e', '#ec9263', '#a3d3f8'
                  ];
                  return colorList[params.dataIndex]
                }
              }
            }
          }
        ]
      }
      return option
    },
    handleSelectionChange(val) {
      this.multipleSelection = val;
    },
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`);
    },

    //导出错误
    exportFile () {
      this.$notify.error({
        title: '提示',
        message: '这是一条错误的提示消息'
      });
    }
  },
  mounted () {
    var myChart = echarts.init(document.getElementById('myChart'))
    myChart.setOption(this.init())
    window.onresize = myChart.resize;
  }
}
</script>

<style lang="scss" scoped>
.mianbaoxie {
  padding: 20px 0 20px 15px;
  box-sizing: border-box;
  border-bottom: 1px solid #eee;
  cursor: pointer;
  .link {
    /deep/ span {
      font-size: 18px;
      font-weight: 800;
    }
  }
}

.select_time {
  padding: 15px 0 0px 15px;
  display: flex;
  justify-content: space-between;
  > span {
    display: flex;
  }
  .block {
    display: flex;
  }
  .time {
    /deep/ .el-form-item__label {
      &::before {
        display: inline-block;
        content: '';
        width: 4px;
        height: 14px;
        background: #fe8130;
        margin-right: 10px;
      }
    }
  }
}

.flex {
  display: flex;
  padding: 0 15px;
  height: 85%;
  box-sizing: border-box;
  .left {
    width: 40%;
    height: 100%;
    border: 3px solid #f0f0f0;
    .title {
      line-height: 45px;
      font-weight: bold;
      padding-left: 15px;
      font-size: 18px;
      background: #f9f9f9;
      border: 3px solid #f0f0f0;
      border-left: none;
      border-right: none;
      border-top: none;
    }
    .rate {
      display: flex;
      align-items: center;
      justify-content: center;
      height: 95%;
    }
  }
  .right {
    flex: 1;
    height: 100%;
    margin-right: 25px;
    .table {
      height: 89%;
      overflow: auto;
    }
    .page {
      text-align: center;
      margin-top: 12px;
      padding: 5px 0 0 0;
    }
  }
}

</style>