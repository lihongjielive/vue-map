<template>
  <div>
    {{msg}}
    <!-- 初始化echart需要有个宽高的盒子 -->
    <div class="hello" ref="mapbox"></div>
  </div>
</template>

<script>
import echarts from 'echarts'
//要使用地图,先注册
import 'echarts/map/js/china'
//调用jsonp接口
import jsonp from 'jsonp'
//设置配置项
const option = {
   //标题
   title: {
     text: '疫情分布'
   },
   //渲染
   series: [{
     name: '确证人数',
     type: 'map',  //告诉echarts要去渲染一个地图
     map: 'china',  //渲染哪个地体
     label: {
       show: true,   //控制对应地区得汉字
       color: 'red',   //颜色
       fontSize: 10   //字体大小
     },
     itemStyle: {
       //地图板块颜色和边框
       areaColor: '#eee',
       borderColor: '#2c3e50'
     },
     raom: true, //滚轮滚动可以放大缩小及拖动
     zoom: 1.2,  //控制地图得放大缩小
     emphasis: {
       //控制鼠标滑过之后得背景色和字体颜色
       label: {
         color: '#fff',
         fontSize: 12
       },
       itemStyle: {
         areaColor: '#83b5e7'
       }
     },
     data: [   //用来展示后台数据,{name:XXX,value:xxx}

     ] 
   }],
   visualMap: [{
     type: 'piecewise',
     show: true,
     //splitNumber: 5,  //分为5段
     pieces: [
       //分段
        {min: 10000}, // 不指定 max，表示 max 为无限大（Infinity）。
        {min: 10000, max: 9999},
        {min: 100, max: 999},
        {min: 10, max: 99},
        {min: 1, max: 9}    // 不指定 min，表示 min 为无限大（-Infinity）。
    ],
    align: 'left',  //默认是left
    orient: 'vertical',
    showLabel: true,
    textStyle: {
      fontSize: 12  //控制字体大小
    },
    inRange: {
      symbol: 'rect',
      color: ['#ffc0b1','#9c0505']
    },
    itemWidth: 20,
    itemHeight: 10
   }],
   tooltip: {
     trigger: 'item',
     formatter: '{b}<br/>{c} ( 人 )'
   },
   toolbox: {
     show: true,
     orient: 'vertical',
     left: 'right',
     top: 'center',
     feature: {
        dataView: {readOnly: false},
         restore: {},
         saveAsImage: {}
       }
   }
}
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  mounted() {
    this.getData();
    //this.$refs用来获取元素或组件
    this.mychart = echarts.init(this.$refs.mapbox);
    this.mychart.setOption(option);
  },
  methods: {
    getData(){
      jsonp('https://interface.sina.cn/news/wap/fymap2020_data.d.json?_=1580892522427',{},(err,data)=>{
        if(!err){
          //数据请求成功
          let list = data.data.list.map(item=>({name:item.name,value:item.value}));
          option.series[0].data = list;
          //这行代码执行得前提是dom已渲染完成,echarts执行初始化
          this.mychart.setOption(option);
        }
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .hello {
    height: 500px;
    width: 6oopx;
  }
</style>
