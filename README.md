# sxfloeecharts
绍兴流量曲线预览页
线上地址：https://volodya-01.github.io/sxflowechartsviewpage/

> A Vue.js project

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).
Echarts中legend图例默认只有一个为选中状态 https://blog.csdn.net/wlangmood/article/details/68490751
方法：使用legend.selected，例的选中状态，让对应data[i]的值为false
代码：

    legend: {
                     x: 'left',
                     data: ['送风温度', '回风温度', '室外温度', '室外湿度', '', '室内1温度', '室内1湿度', '室内2温度', '室内2湿度'],
    				 inactiveColor: '#999',
    				 //selectedMode: 'single',
    				 selected: {
    					'回风温度': false,
    					'室外温度': false,
    					'室外湿度': false,
    					'室内1温度': false,
    					'室内1湿度': false,
    					'室内2温度': false,
    					'室内2湿度': false
    				}
                 }
echarts图例legend 限制最少显示一个图例，遇到一个需求，就是如何保证echarts图表里至少显示一个图例的数据（也就是最后一个图例不能变成unselected的状态，下图是最初加载时的画面  https://segmentfault.com/q/1010000015843002
myChart.on('legendselectchanged', function (params) {
        let option = this.getOption();
        let select_key = Object.keys(params.selected);
        let select_value = Object.values(params.selected);
        console.log('select_value',select_value,'length',select_value.length)
        var n = 0;
        select_value.map(res => {
            if(!res){
                n++;
            }
        });
        console.log('n',n)
        if( n ==select_value.length){
            option.legend[0].selected[params.name] = true;
        }
                            
        this.setOption(option)
});
