<template>
  <div>
    <!-- 面包屑导航区域 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>数据统计</el-breadcrumb-item>
      <el-breadcrumb-item>数据报表</el-breadcrumb-item>
    </el-breadcrumb>

    <!-- 卡片视图区域 -->
    <el-card>
      <!-- 2. 为ECharts准备一个具备大小（宽高）的Dom -->
      <div id="main1" style="width: 500px; height: 400px" class="chart"></div>
      <div id="main2" style="width: 590px; height: 400px" class="chart"></div>
      <div id="main3" style="width: 1200px; height: 600px" class="chart"></div>
    </el-card>
  </div>
</template>

<script>
// 1. 导入 echarts
import echarts from 'echarts'
import _ from 'lodash'

export default {
  data() {
    return {
      options1: {
        title: {
          text: '用户来源',
          subtext: '',
          left: 'center'
        },
        tooltip: {
          trigger: 'item'
        },
        legend: {
          orient: 'vertical',
          left: 'left'
        },
        series: [
          {
            name: '用户来源',
            type: 'pie',
            radius: '50%',
            data: [
              { value: 1048, name: '华东' },
              { value: 735, name: '华南' },
              { value: 580, name: '华北' },
              { value: 484, name: '西部' },
              { value: 300, name: '其他' }
            ],
            emphasis: {
              itemStyle: {
                shadowBlur: 10,
                shadowOffsetX: 0,
                shadowColor: 'rgba(0, 0, 0, 0.5)'
              }
            }
          }
        ]
      },
      options2: {
        tooltip: {
          trigger: 'item'
        },
        legend: {
          top: '5%',
          left: 'center'
        },
        series: [
          {
            name: '访问来源',
            type: 'pie',
            radius: ['40%', '70%'],
            avoidLabelOverlap: false,
            itemStyle: {
              borderRadius: 10,
              borderColor: '#fff',
              borderWidth: 2
            },
            label: {
              show: false,
              position: 'center'
            },
            emphasis: {
              label: {
                show: true,
                fontSize: '40',
                fontWeight: 'bold'
              }
            },
            labelLine: {
              show: false
            },
            data: [
              { value: 1048, name: '衬衫' },
              { value: 735, name: '洗面奶' },
              { value: 580, name: '电视机' },
              { value: 484, name: '牛奶' },
              { value: 300, name: '面膜' }
            ]
          }
        ]
      },
      options3: {
        legend: {},
        tooltip: {
          trigger: 'axis',
          showContent: false
        },
        dataset: {
          source: [
            ['product', '5月', '6月', '7月', '8月', '9月', '10月'],
            ['男士修身款商务休闲男士衬衣', 5650, 8210, 8870, 6010, 5340, 8510],
            ['茵曼秋装女小清新文艺范印花纯棉白衬衫', 5110, 5140, 5510, 5330, 7380, 6870],
            ['韩版女装秋装新宽松显瘦纯色系带长袖衬衫', 4010, 6220, 6950, 3640, 4520, 3250],
            ['新品女式加绒加厚长袖衬衫', 2520, 3710, 4120, 1800, 3390, 4910]
          ]
        },
        xAxis: { type: 'category' },
        yAxis: { gridIndex: 0 },
        grid: { top: '55%' },
        series: [
          { type: 'line', smooth: true, seriesLayoutBy: 'row', emphasis: { focus: 'series' } },
          { type: 'line', smooth: true, seriesLayoutBy: 'row', emphasis: { focus: 'series' } },
          { type: 'line', smooth: true, seriesLayoutBy: 'row', emphasis: { focus: 'series' } },
          { type: 'line', smooth: true, seriesLayoutBy: 'row', emphasis: { focus: 'series' } },
          {
            type: 'pie',
            id: 'pie',
            radius: '30%',
            center: ['50%', '25%'],
            emphasis: { focus: 'data' },
            label: {
              formatter: '{b}: {@5月} ({d}%)'
            },
            encode: {
              itemName: 'product',
              value: '5月',
              tooltip: '5月'
            }
          }
        ]
      }
    }
  },
  created() {},
  // 此时，页面上的元素，已经被渲染完毕了！
  async mounted() {
    // 3. 基于准备好的dom，初始化echarts实例
    var myChart1 = echarts.init(document.getElementById('main1'))
    var myChart2 = echarts.init(document.getElementById('main2'))
    var myChart3 = echarts.init(document.getElementById('main3'))
    const { data: res } = await this.$http.get('reports/type/1')

    if (res.meta.status !== 200) {
      return this.$message.error('获取折线图数据失败！')
    }

    // 4. 准备数据和配置项
    // const result = _.merge(res.data, this.options)
    const result1 = _.merge(this.options1)
    const result2 = _.merge(this.options2)
    const result3 = _.merge(this.options3)
    // 5. 展示数据
    myChart1.setOption(result1)
    myChart2.setOption(result2)
    myChart3.on('updateAxisPointer', function (event) {
      var xAxisInfo = event.axesInfo[0]
      if (xAxisInfo) {
        var dimension = xAxisInfo.value + 1
        myChart3.setOption({
          series: {
            id: 'pie',
            label: {
              formatter: '{b}: {@[' + dimension + ']} ({d}%)'
            },
            encode: {
              value: dimension,
              tooltip: dimension
            }
          }
        })
      }
    })
    myChart3.setOption(result3)
  },
  methods: {}
}
</script>

<style lang="less" scoped>
.chart {
  display: inline-block;
}
</style>
