<template>
    <div class="box">
      <!-- 地图容器，使用 ref 获取 DOM 元素 -->
      <div class="title"></div>
      <div class="map" ref="chinaMap" style="width: 120vh; height: 80vh;"></div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  import * as echarts from 'echarts';
  import chinaJSON from './json/china.json'; // 导入中国地图的 JSON 数据
  import "./css/style.css"; // 导入样式文件
  
  // 定义散点数据，包括城市名称、经纬度和平均海拔
const data = [
    { name: "北京市", value: [116.2351, 39.5424, 43] },
    { name: "深圳市", value: [114.271522, 22.753644, 12] },
    { name: "云南省", value: [102.7123, 25.0406, 1980] },
    { name: "上海市", value: [121.4737, 31.2304, 4] },
    { name: "广东省", value: [113.2644, 23.1291, 50] },
    { name: "四川省", value: [104.0668, 30.5728, 500] },
    { name: "湖北省", value: [114.3052, 30.5728, 121] },
    { name: "天津市", value: [117.1902, 39.1256, 5] },
    { name: "重庆市", value: [106.5516, 29.5630, 400] },
    { name: "河北省", value: [114.5025, 38.0455, 100] },
    { name: "山西省", value: [112.5493, 37.8570, 800] },
    { name: "内蒙古自治区", value: [111.7519, 40.8415, 1010] },
    { name: "辽宁省", value: [123.4315, 41.8057, 220] },
    { name: "吉林省", value: [125.3245, 43.8868, 400] },
    { name: "黑龙江省", value: [126.6425, 45.7560, 150] },
    { name: "江苏省", value: [118.7969, 32.0603, 5] },
    { name: "浙江省", value: [120.1551, 30.2741, 90] },
    { name: "安徽省", value: [117.2830, 31.8612, 50] },
    { name: "福建省", value: [119.3062, 26.0753, 300] },
    { name: "江西省", value: [115.8582, 28.6820, 140] },
    { name: "山东省", value: [117.0009, 36.6758, 100] },
    { name: "河南省", value: [113.6654, 34.7570, 150] },
    { name: "湖南省", value: [112.9388, 28.2282, 300] },
    { name: "海南省", value: [110.3312, 20.0313, 50] },
    { name: "贵州省", value: [106.7135, 26.5783, 1100] },
    { name: "广西壮族自治区", value: [108.3275, 22.8155, 250] },
    { name: "西藏自治区", value: [91.1409, 29.6456, 4500] },
    { name: "陕西省", value: [108.9480, 34.2632, 800] },
    { name: "甘肃省", value: [103.8236, 36.0580, 1500] },
    { name: "青海省", value: [101.7789, 36.6232, 3000] },
    { name: "宁夏回族自治区", value: [106.2782, 38.4664, 1100] },
    { name: "新疆维吾尔自治区", value: [87.6177, 43.7928, 1500] },
    { name: "台湾省", value: [121.5091, 25.0443, 1000] },
    { name: "香港特别行政区", value: [114.1694, 22.3193, 41] },
    { name: "澳门特别行政区", value: [113.5439, 22.1987, 6] },
    { name: "南海诸岛", value: [112.3253, 16.8414, 2] }
];
  
  // 定义地图容器的引用
  const chinaMap = ref();
  
  // 在组件挂载后调用绘制中国地图的函数
  onMounted(() => {
    drawChina();
  });
  
  // 定义区域和散点数据
  let position = data.map(item => ({ name: item.name, value: item.value }));
  let heights = data.map (heights => heights.value[2]);

  
  // 绘制中国地图的函数
  function drawChina() {
    // 初始化 ECharts 实例
    var myChart = echarts.init(chinaMap.value);
  
    // 注册中国地图
    echarts.registerMap('china', chinaJSON);
  
    // 配置 ECharts 选项
    var option = {
      tooltip: {
        show: true,
        trigger: 'item',
        formatter: function (params) {
          // console.log(params); // 调试输出
          const elevation = params.data.value[2];
          return `
            <div style="text-align: center; font-size: 14px; line-height: 1.5;">
              <b style="color: #1685a9;">${params.name}</b><br/>
              平均海拔: <span style="color: #1e90ff;">${elevation} 米</span>
            </div>
          `;
        },
      },
      visualMap: { 
        min: 0,
        max: 5000,
        left: 'left',
        top: 'bottom',
        itemWidth: 10,
        itemHeight: 100,
        text: ['高', '低'], // 文本，默认为数值文本
        calculable: true,
        inRange: {
          color: ['#b5f5ec', '#008685'] // 从低到高的渐变色
        }
      },
      title: {
        show: true,
        text: '中国地图',
        x: 'center',
        textStyle: {
          color: '#3fc1c9',
        }
      },
      geo: {
        map: 'china',
        zoom: 1.2, // 初始化缩放大小
      },
      series: {
        type: 'map',
        map: 'china',
        data: position,
        roam: false, // 是否允许缩放和拖拽
        zoom: 1.2, // 初始化缩放大小
        scaleLimit: { // 缩放限制
          min: 1, // 最小缩放
          max: 2, // 最大缩放
        },
        itemStyle: {
          areaColor: '#71c9ce',
          color: '#cbf1f5',
          borderColor: '#a6e3e9',
          borderWidth: 1,
        },
        emphasis: { // 高亮状态
          itemStyle: {
            areaColor: '#69c0ff', // 鼠标移入时的区域颜色
            borderColor: '#fff', // 鼠标移入时的边界颜色
            borderWidth: 2, // 鼠标移入时的边界宽度
          },
        },
        label: {
          show: true,
          color: '#40514e',
          fontSize: '10px',
        },
      },
    };
  
    // 设置 ECharts 选项
    myChart.setOption(option);
     // 监听点击事件
  myChart.on('click', function (params) {
    // 获取点击的地区名称
    const regionName = params.name;
    // 根据点击的地区名称设置背景图片
    document.body.style.background= `url('src/assets/images/${regionName}.jpg')`;
    document.body.style.backgroundSize = 'cover';
  });
  }
  </script>
  
  <style src="./css/style.css" scoped></style>
  