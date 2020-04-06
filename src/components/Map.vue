<template>
  <div class="map">
    <div id="main" style="width:1000px; height: 700px;"></div>
  </div>
</template>

<script>
import echarts from 'echarts'
import jsonp from 'jsonp'
import 'echarts/map/js/china'

const options = {
  title: {
    text: '疫情地图',
    x: 'center',
    textStyle: {
      color: '#9c0505'
    }
  },
  // 数据
  series: [
    {
      type: 'map', // 告诉echarts 渲染地图
      map: 'china',
      label: {
        show: true, // 显示各个省份名称
        fontSize: 8,
        color: 'red'
      },
      itemStyle: {
        areaColor: '#fff', // 区域的背景颜色
        borderColor: 'blue'
      },
      emphasis: {
        // 控制鼠标滑过时的高亮样式
        itemStyle: {
          areaColor: '#c7fffd'
        }
      },
      zoom: 1.2 // 控制地图的大小
    }
  ],
  visualMap: [
    {
      type: 'piecewise',
      show: true,
      splitNumber: 6,
      pieces: [
        {
          min: 10000
        }, // 不指定 max，表示 max 为无限大（Infinity）。
        {
          min: 1000,
          max: 9999
        },
        {
          min: 100,
          max: 999
        },
        {
          min: 10,
          max: 99
        },
        {
          min: 1,
          max: 9
        },
        {
          min: 0,
          max: 0
        }
      ],
      // align:'right' // 默认是left
      inRange: {
        symbol: 'rect',
        color: ['#fff', '#ffaa85', '#ff7b69', '#cc2929', '#8c0d0d', '#660208']
      },
      itemHeight: 10,
      itemWidth: 20
    }
  ],
  tooltip: {
    trigger: 'item',
    formatter: '地区:{b}<br/>确诊:{c}'
  }
}

export default {
  data() {
    return {
      myChart: ''
    }
  },
  mounted() {
    this.getData()
    this.init()
  },
  methods: {
    init() {
      this.myChart = echarts.init(document.getElementById('main'))

      // 使用刚指定的配置项和数据显示图表。
    },
    getData() {
      // eslint-disable-next-line handle-callback-err
      jsonp(
        'http://interface.sina.cn/news/wap/fymap2020_data.d.json?_=1580892522427',
        // eslint-disable-next-line handle-callback-err
        (err, data) => {
          console.log(data.data.list)
          var lists = data.data.list.map(item => {
            return { name: item.name, value: item.value }
          })
          console.log(lists)
          lists.push({ name: '南海诸岛', value: 0 })
          options.series[0].data = lists
          this.myChart.setOption(options)
        }
      )
    }
  }
}
</script>
