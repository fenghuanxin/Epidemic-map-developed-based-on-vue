<template>
	<div class="hello">
		<!-- 初始化echarts 需要个有宽高的盒子 -->
		<div ref="mapbox" class="box">
		</div>
	</div>
</template>

<script>
	import echarts from 'echarts'
	import 'echarts/map/js/china.js'
	import jsonp from 'jsonp'

  //  使用地图 需要先注册地图
  const option = {
    title: {
      text: '疫情地图'
    },
    series: [{
      type: 'map',  // 告诉 echarts 渲染地图
      map: 'china', // 告诉 echarts 渲染中国地图
      label: {
        show: true, //  显示各个省份名称
        fontSize: 8
      },
      itemStyle: {
        areaColor: '#fff' //  区域的背景颜色
      },
      emphasis: {
        //  控制鼠标滑过之后的背景色和字体颜色
        itemStyle: {
          areaColor: '#c7fffd'
        }
      },
      zoom: 1.2,  //控制地图大小
    }],
    visualMap: [{
      type: 'piecewise',
      show: true,
      splitNumber: 6,
      pieces: [
        {
          min: 10000
        },
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
      
      //  align: 'right'  //  默认left
      //  orient: 'horizontal' // 默认竖直
      //  left right 这些属性控制 分段所在位置
      //  showLabel: false //  默认显示数值
      //  textStyle: {}
      inRange: {
        symbol: 'rect',
        color: ['#fff', '#ffaa85', '#ff7b69', '#cc2929', '#8c0d0d', '#660208']  // 控制分段数值颜色从哪变哪
      },
      itemWidth: 20,
      itemHeight: 10
    }],
    tooltip: {
      // position: function (pos, params, dom, rect, size) {
			//       // 鼠标在左侧时 tooltip 显示到右侧，鼠标在右侧时 tooltip 显示到左侧。
			//       var obj = {top: 60};
			//       obj[['left', 'right'][+(pos[0] < size.viewSize[0] / 2)]] = 5;
			//       return obj;
			//   }    
    },
  };

  export default {
    name: 'Map',
    mounted() {
      this.getData();
      this.mychart = echarts.init(this.$refs.mapbox);
      this.mychart.setOption(option);
    },
    methods: {
      getData() {
        jsonp('https://interface.sina.cn/news/wap/fymap2020_data.d.json?_=1580892522427',{},(err, data) => {
          var list = data.data.list.map(item => {
            return {
              name: item.name,
              value: item.value
            }
          })
          console.log(list)
          option.series[0].data = list;
          this.mychart.setOption(option);
        })
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
	.box{
		width: 750px;
		height: 650px;
		margin: auto;
		/* border: 2px solid aquamarine; */
	}
</style>
