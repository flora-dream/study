# Echarts v5 学习
## 初始化
- 指定容器大小（推荐）
- 在图表初始化时指定大小（不推荐）
```
<div id="main" style="width: 600px;height:400px;"></div>
<script type="text/javascript">
  var myChart = echarts.init(document.getElementById('main'), null, {
    width: 600,
    height: 400
  });
</script>
```
## eInstance.resize() 
指定大小和相应变化

## eInstance.dispose()
销毁实例

## 主题Theme
```
var chart = echarts.init(dom, 'dark');
```
- UMD 格式的 JS 文件，文件内部已经做了自注册，直接引入
- 如果主题保存为 JSON 文件，则需要自行加载和注册

## 样式设置
itemStyle, lineStyle, areaStyle, label, ...

## 高亮样式
emphasis

## 数据集
- 数组格式
```js
  option = {
  legend: {},
  tooltip: {},
  dataset: {
  // 提供一份数据。
  source: [
  ['product', '2015', '2016', '2017'],
  ['Matcha Latte', 43.3, 85.8, 93.7],
  ['Milk Tea', 83.1, 73.4, 55.1],
  ['Cheese Cocoa', 86.4, 65.2, 82.5],
  ['Walnut Brownie', 72.4, 53.9, 39.1]
  ]
  },
  // 声明一个 X 轴，类目轴（category）。默认情况下，类目轴对应到 dataset 第一列。
  xAxis: { type: 'category' },
  // 声明一个 Y 轴，数值轴。
  yAxis: {},
  // 声明多个 bar 系列，默认情况下，每个系列会自动对应到 dataset 的每一列。
  series: [{ type: 'bar' }, { type: 'bar' }, { type: 'bar' }]
  };
```
- 对象格式
```js
option = {
  legend: {},
  tooltip: {},
  dataset: {
    // 用 dimensions 指定了维度的顺序。直角坐标系中，如果 X 轴 type 为 category，
    // 默认把第一个维度映射到 X 轴上，后面维度映射到 Y 轴上。
    // 如果不指定 dimensions，也可以通过指定 series.encode
    // 完成映射，参见后文。
    dimensions: ['product', '2015', '2016', '2017'],
    source: [
      { product: 'Matcha Latte', '2015': 43.3, '2016': 85.8, '2017': 93.7 },
      { product: 'Milk Tea', '2015': 83.1, '2016': 73.4, '2017': 55.1 },
      { product: 'Cheese Cocoa', '2015': 86.4, '2016': 65.2, '2017': 82.5 },
      { product: 'Walnut Brownie', '2015': 72.4, '2016': 53.9, '2017': 39.1 }
    ]
  },
  xAxis: { type: 'category' },
  yAxis: {},
  series: [{ type: 'bar' }, { type: 'bar' }, { type: 'bar' }]
};
```

- 数据到图形的映射
```js
var option = {
  dataset: {
    source: [
      ['score', 'amount', 'product'],
      [89.3, 58212, 'Matcha Latte'],
      [57.1, 78254, 'Milk Tea'],
      [74.4, 41032, 'Cheese Cocoa'],
      [50.1, 12755, 'Cheese Brownie'],
      [89.7, 20145, 'Matcha Cocoa'],
      [68.1, 79146, 'Tea'],
      [19.6, 91852, 'Orange Juice'],
      [10.6, 101852, 'Lemon Juice'],
      [32.7, 20112, 'Walnut Brownie']
    ]
  },
  xAxis: {},
  yAxis: { type: 'category' },
  series: [
    {
      type: 'bar',
      encode: {
        // 将 "amount" 列映射到 X 轴。
        x: 'amount',
        // 将 "product" 列映射到 Y 轴。
        y: 'product'
      }
    }
  ]
};
```
- `seriesLayoutBy: 'row'` 改变维度分割方向

## transform数据转换
- 在一个空的 dataset 中声明 transform, fromDatasetIndex/fromDatasetId 来表示我们要生成新的数据。
- `fromDatasetIndex: 0` 表示输入来自于 index 为 `0` 的 dataset 。又例如，
- `fromDatasetId: 'a'` 表示输入来自于 `id: 'a'` 的 dataset。
- 当这些属性都不指定时，默认认为，输入来自于 index 为 `0` 的 dataset 。
- transform 可以被链式声明，这是一个语法糖。
```js
      // 几个 transform 被声明成 array ，他们构成了一个链，
      // 前一个 transform 的输出是后一个 transform 的输入。
      transform: [
        {
          type: 'filter',
          config: { dimension: 'Product', value: 'Tofu' }
        },
        {
          type: 'sort',
          config: { dimension: 'Year', order: 'desc' }
        }
      ]

```
- 一个transform输出多个data
```js
      {
      // 这个 dataset 的 index 为 `1`。
      transform: {
        type: 'boxplot'
      }
      // 这个 "boxplot" transform 生成了两个数据：
      // result[0]: boxplot series 所需的数据。
      // result[1]: 离群点数据。
      // 当其他 series 或者 dataset 引用这个 dataset 时，他们默认只能得到
      // result[0] 。
      // 如果想要他们得到 result[1] ，需要额外声明如下这样一个 dataset ：
    },
    {
      // 这个 dataset 的 index 为 `2`。
      // 这个额外的 dataset 指定了数据来源于 index 为 `1` 的 dataset。
      fromDatasetIndex: 1,
      // 并且指定了获取 transform result[1] 。
      fromTransformResult: 1
    }

```
- 在开发环境中 debug ,在transform里配置`print: true`

## 数据转换器 "filter"
```js
    transform: {
        type: 'filter',
        config: { dimension: 'Year', '=': 2011 }
        // 这个筛选条件表示，遍历数据，筛选出维度（ dimension ）
        // 'Year' 上值为 2011 的所有数据项。
    }
```
- dimension可以是维度名也可以是index
- 关系比较符有： >（gt）、>=（gte）、<（lt）、<=（lte）、=（eq）、!=（ne、<>）、reg。
- 支持了逻辑比较操作符 与或非（ and | or | not ），可以嵌套
```js
transform: {
  type: 'filter',
  config: {
    or: [{
      and: [{
        dimension: 'Price', '>=': 10, '<': 20
      }, {
        dimension: 'Sales', '<': 100
      }, {
        not: { dimension: 'Product', '=': 'Tofu' }
      }]
    }, {
      and: [{
        dimension: 'Price', '>=': 10, '<': 20
      }, {
        dimension: 'Sales', '<': 100
      }, {
        not: { dimension: 'Product', '=': 'Cake' }
      }]
    }]
  }
}
```
- 解析器parser: 'time'、'trim'、'number'。number比较宽松（例如 '33%', 12px可以直接比较）

## 数据转换器 "sort"
- 可以多重排序。
- 默认按照数值大小排序
- 对于其他“不能转为数值的字符串”，也能在它们之间按字符串进行排序。
- 当“数值及可转为数值的字符串”和“不能转为数值的字符串”进行排序时，或者它们和“其他类型的值”进行比较时，它们本身是不知如何进行比较的。那么我们称呼“后者”为“incomparable”，并且可以设置 incomparable: 'min' | 'max' 来指定一个“incomparable”在这个比较中是最大还是最小，从而能使它们能产生比较结果。这个设定的用途，比如可以是，决定空值（例如 null, undefined, NaN, '', '-'）在排序的头还是尾。
- 解析器 parser: 'time' | 'trim' | 'number' 可以被使用
- 注册外部数据转换器`echarts.registerTransform(ecStatTransform(ecStat).regression);`
```js
option = {
  dataset: [
    {
      dimensions: ['name', 'age', 'profession', 'score', 'date'],
      source: [
        [' Hannah Krause ', 41, 'Engineer', 314, '2011-02-12'],
        ['Zhao Qian ', 20, 'Teacher', 351, '2011-03-01'],
        [' Jasmin Krause ', 52, 'Musician', 287, '2011-02-14'],
        ['Li Lei', 37, 'Teacher', 219, '2011-02-18'],
        [' Karle Neumann ', 25, 'Engineer', 253, '2011-04-02'],
        [' Adrian Groß', 19, 'Teacher', null, '2011-01-16'],
        ['Mia Neumann', 71, 'Engineer', 165, '2011-03-19'],
        [' Böhm Fuchs', 36, 'Musician', 318, '2011-02-24'],
        ['Han Meimei ', 67, 'Engineer', 366, '2011-03-12']
      ]
    },
    {
      transform: {
        type: 'sort',
        config: [
          // 对两个维度按声明的优先级分别排序。
          { dimension: 'profession', order: 'desc' },
          { dimension: 'score', order: 'desc' }
        ]
      }
    }
  ],
  series: {
    type: 'bar',
    datasetIndex: 1
  }
  //...
};
```

## 坐标轴
- 单个 grid 组件最多只能放两个 x/y 轴，多于两个 x/y 轴需要通过配置 offset 属性防止同个位置多个轴的重叠。两个 x 轴显示在上下，两个 y 轴显示在左右两侧。
```js
option = {
  xAxis: {
    type: 'time',
    name: '销售时间'
    // ...
  },
  yAxis: [
    {
      type: 'value',
      name: '销售数量'
      // ...
    },
    {
      type: 'value',
      name: '销售金额'
      // ...
    }
  ]
  // ...
};
```
- x 轴和 y 轴都由轴线 axisLine 、刻度 axisTick、刻度标签 axisLabel、轴标题四个部分组成。
```js
option = {
  tooltip: {
    trigger: 'axis',
    axisPointer: { type: 'cross' }
  },
  legend: {},
  xAxis: [
    {
      type: 'category',
      axisTick: {
        alignWithLabel: true
      },
      data: [
        '1月',
        '2月',
        '3月',
        '4月',
        '5月',
        '6月',
        '7月',
        '8月',
        '9月',
        '10月',
        '11月',
        '12月'
      ]
    }
  ],
  yAxis: [
    {
      type: 'value',
      name: '降水量',
      min: 0,
      max: 250,
      position: 'right',
      axisLabel: {
        formatter: '{value} ml'
      }
    },
    {
      type: 'value',
      name: '温度',
      min: 0,
      max: 25,
      position: 'left',
      axisLabel: {
        formatter: '{value} °C'
      }
    }
  ],
  series: [
    {
      name: '降水量',
      type: 'bar',
      yAxisIndex: 0,
      data: [6, 32, 70, 86, 68.7, 100.7, 125.6, 112.2, 78.7, 48.8, 36.0, 19.3]
    },
    {
      name: '温度',
      type: 'line',
      smooth: true,
      yAxisIndex: 1,
      data: [
        6.0,
        10.2,
        10.3,
        11.5,
        10.3,
        13.2,
        14.3,
        16.4,
        18.0,
        16.5,
        12.0,
        5.2
      ]
    }
  ]
};
```
## visualMap 组件
- visualMap 组件定义了把数据的哪个维度映射到什么视觉元素上
- type: 'continuous' // 定义为连续型 visualMap
- type: 'piecewise' // 定义为分段型 visualMap
```js
option = {
  visualMap: [
    {
      type: 'continuous',
      min: 0,
      max: 5000,
      dimension: 3, // series.data 的第四个维度（即 value[3]）被映射
      seriesIndex: 4, // 对第四个系列进行映射。
      inRange: {
        // 选中范围中的视觉配置
        color: ['blue', '#121122', 'red'], // 定义了图形颜色映射的颜色列表，
        // 数据最小值映射到'blue'上，
        // 最大值映射到'red'上，
        // 其余自动线性计算。
        symbolSize: [30, 100] // 定义了图形尺寸的映射范围，
        // 数据最小值映射到30上，
        // 最大值映射到100上，
        // 其余自动线性计算。
      },
      outOfRange: {
        // 选中范围外的视觉配置
        symbolSize: [30, 100]
      }
    }
    //    ...
  ]
};
```

## 鼠标事件处理
- ECharts 支持常规的鼠标事件类型，包括 'click'、 'dblclick'、 'mousedown'、 'mousemove'、 'mouseup'、 'mouseover'、 'mouseout'、 'globalout'、 'contextmenu' 事件。
```js
// 处理点击事件并且跳转到相应的百度搜索页面
myChart.on('click', function(params) {
  window.open('https://www.baidu.com/s?wd=' + encodeURIComponent(params.name));
});

```
- params参数
```js
type EventParams = {
  // 当前点击的图形元素所属的组件名称，
  // 其值如 'series'、'markLine'、'markPoint'、'timeLine' 等。
  componentType: string;
  // 系列类型。值可能为：'line'、'bar'、'pie' 等。当 componentType 为 'series' 时有意义。
  seriesType: string;
  // 系列在传入的 option.series 中的 index。当 componentType 为 'series' 时有意义。
  seriesIndex: number;
  // 系列名称。当 componentType 为 'series' 时有意义。
  seriesName: string;
  // 数据名，类目名
  name: string;
  // 数据在传入的 data 数组中的 index
  dataIndex: number;
  // 传入的原始数据项
  data: Object;
  // sankey、graph 等图表同时含有 nodeData 和 edgeData 两种 data，
  // dataType 的值会是 'node' 或者 'edge'，表示当前点击在 node 还是 edge 上。
  // 其他大部分图表中只有一种 data，dataType 无意义。
  dataType: string;
  // 传入的数据值
  value: number | Array;
  // 数据图形的颜色。当 componentType 为 'series' 时有意义。
  color: string;
};
```
- `chart.on(eventName, query, handler);`。query 可为 string 或者 Object。如果为 Object，可以包含以下一个或多个属性，每个属性都是可选的：
```js
{
  ${mainType}Index: number // 组件 index
  ${mainType}Name: string // 组件 name
  ${mainType}Id: string // 组件 id
  dataIndex: number // 数据项 index
  name: string // 数据项 name
  dataType: string // 数据项 type，如关系图中的 'node', 'edge'
  element: string // 自定义系列中的 el 的 name
}
```
```js
chart.setOption({
  // ...
  series: [
    {
      // ...
    },
    {
      // ...
      data: [
        { name: 'xx', value: 121 },
        { name: 'yy', value: 33 }
      ]
    }
  ]
});
chart.on('mouseover', { seriesIndex: 1, name: 'xx' }, function() {
  // series index 1 的系列中的 name 为 'xx' 的元素被 'mouseover' 时，此方法被回调。
});
```

## 组件交互行为
```js
// 图例开关的行为只会触发 legendselectchanged 事件
myChart.on('legendselectchanged', function(params) {
  // 获取点击图例的选中状态
  var isSelected = params.selected[params.name];
  // 在控制台中打印
  console.log((isSelected ? '选中了' : '取消选中了') + '图例' + params.name);
  // 打印所有图例的状态
  console.log(params.selected);
});
```
- ECharts 通过调用 myChart.dispatchAction({ type: '' }) 触发图表行为，统一管理所有动作
- zrender 事件与 echarts 事件不同。前者是当鼠标在任何地方都会被触发，而后者是只有当鼠标在图形元素上时才能被触发。
```js
myChart.getZr().on('click', function(event) {
  // 没有 target 意味着鼠标/指针不在任何一个图形元素上，它是从“空白处”触发的。
  if (!event.target) {
    // 点击在了空白处，做些什么。
  }
});
```

## 服务端渲染 ECharts 图表
- 需要缩短前端的渲染时间，保证第一时间显示图表 。需要在 Markdown, PDF 等不支持动态运行脚本的环境中嵌入图表













## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).




