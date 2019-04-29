<template>
  <div :class="className" :style="{height:height,width:width}"/>
</template>

<script>
import Bus from "@/bus.js";
import { debounce } from "@/utils";
export default {
  name: "echartday",
  props: {
    className: {
      type: String,
      default: "chart"
    },
    width: {
      type: String,
      default: "100%"
    },
    height: {
      type: String,
      default: "380px"
    },
    autoResize: {
      type: Boolean,
      default: true
    }
    /*  chartData: {
      type: Object,
      required: true
    } */
  },
  data() {
    return {
      chart: null,
      sidebarElm: null,
      /*  ysechartsareaflowresdata: [], */
      chartData: {}
    };
  },
  watch: {
    chartData: {
      deep: true,
      handler(val) {
        this.setOptions(val);
      }
    }
  },
  mounted() {
    Bus.$on("areaflowresdata", e => {
      var _this = this;
      /*   _this.ysechartsareaflowresdata = e; */
      var Flowstarttime = e.Flowstarttime;
      var Flowendtime = e.Flowendtime;

      var echartstotaldata = [];
      var totallabel = [];
      for (var i = 0; i < e.RData.length; i++) {
        var CurrentData = [];
        var Before24Data = [];
        var Before48Data = [];
        var CurrentTime = [];
        var Currentlabel = e.RData[i].MeterName + "_今天"+"--"+332+"  "+"↑";
        var Before24label = e.RData[i].MeterName + "_昨天";
        var Before48label = e.RData[i].MeterName + "_前天";
        for (var j = 0; j < e.RData[i].Out.length; j++) {
          CurrentTime.push(e.RData[i].Out[j].CurrentTime);
          CurrentData.push(e.RData[i].Out[j].CurrentData);
          Before24Data.push(e.RData[i].Out[j].Before24Data);
          Before48Data.push(e.RData[i].Out[j].Before48Data);
        }
        echartstotaldata.push(CurrentData, Before24Data, Before48Data);
        totallabel.push(Currentlabel, Before24label, Before48label);
      }
      for (var i = 0; i < CurrentTime.length; i++) {
        if (CurrentTime[i] == Flowstarttime) {
          /*  alert(CurrentTime[i]); */
          var staindex = i - 3;
        }
        if (CurrentTime[i] == Flowendtime) {
          var edindex = i + 3;
        }
      }

      var sta = (staindex / Number(CurrentTime.length)) * 100;
      var ed = (edindex / Number(CurrentTime.length)) * 100;
      console.log("%csta的时间：", "font-size:16px;color:green", staindex);
      /*  console.log("CurrentTime");
      console.log(CurrentTime); */
      /* console.log(e.RData);
      console.log(totallabel);
      console.log(CurrentTime);
      console.log(echartstotaldata);
 */
      console.log("%ctotallabel", "font-size:16px;color:red", totallabel);
      var objlegend = {};
      for (var key in totallabel) {
        if (key == 0) {
          objlegend[totallabel[key]] = true;
        } else {
          objlegend[totallabel[key]] = false;
        }
      }
      console.log("%cobjlegend", "font-size:16px;color:red", objlegend);
      var markdata = [];
      for (var i = 0; i < totallabel.length; i++) {
        markdata.push(i * 2);
      }
      var a = {
        Flowstarttime,
        Flowendtime,
        CurrentTime,
        totallabel,
        echartstotaldata,
        sta,
        ed,
        objlegend,
        markdata
      };
      this.chartData = a;
      this.$nextTick(this.initChart());
    });
    if (this.autoResize) {
      this.__resizeHandler = debounce(() => {
        if (this.chart) {
          this.chart.resize();
        }
      }, 100);
      window.addEventListener("resize", this.__resizeHandler);
    }

    // 监听侧边栏的变化
    this.sidebarElm = document.getElementsByClassName("sidebar-container")[0];
    this.sidebarElm &&
      this.sidebarElm.addEventListener(
        "transitionend",
        this.sidebarResizeHandler
      );
  },
  beforeDestroy() {
    if (!this.chart) {
      return;
    }
    if (this.autoResize) {
      window.removeEventListener("resize", this.__resizeHandler);
    }

    this.sidebarElm &&
      this.sidebarElm.removeEventListener(
        "transitionend",
        this.sidebarResizeHandler
      );

    this.chart.dispose();
    this.chart = null;
  },
  methods: {
    sidebarResizeHandler(e) {
      if (e.propertyName === "width") {
        this.__resizeHandler();
      }
    },
    setOptions(chartData) {
      this.chart.setOption({
        grid: {
          width: "92%",
          left: "80"
        },
        legend: {
          type: "scroll",
          width: "80%",
          data: chartData.totallabel,
          inactiveColor: "#999",
          //selectedMode: 'single',
          selected: chartData.objlegend,
          formatter: function(item) {
           /*  for (var i = 0; i < chartData.markdata.length; i++) {
              item = item + chartData.markdata[i];
            }*/
             console.log("%citem", "font-size:16px;color:red", item); 
            /*   if (item=="越城区_今天") {
                        return
                        item.substr(0, 2) + '...' + item.substring(item.length - 2)
                    } */
            /*  item=item+""+chartData.sta */
            return item;
          }
        },
        /* legend: {
          x: "left",
          data: [
            "送风温度",
            "回风温度",
            "室外温度",
            "室外湿度",
            "",
            "室内1温度",
            "室内1湿度",
            "室内2温度",
            "室内2湿度"
          ],
          inactiveColor: "#999",
          //selectedMode: 'single',
           selected:objlegend
          selected: {
            回风温度: false,
            室外温度: false,
            室外湿度: false,
            室内1温度: false,
            室内1湿度: false,
            室内2温度: false,
            室内2湿度: false
          }
        },
 */
        toolbox: {
          right: "60", //距左
          top: "10", //距上
          feature: {
            /*     dataZoom: {
              yAxisIndex: "none"
            }, */
            restore: {}
            /*  saveAsImage: {} */
          }
        },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "cross",
            animation: false,
            label: {
              backgroundColor: "#505765"
            }
          }
        },

        dataZoom: [
          {
            show: true,
            realtime: true,
            start: chartData.sta,
            end: chartData.ed,
            filterMode: "filter",
            /*   labelFormatter:function(params) {
                var newParamsName = String(params).slice(10, 16);
               console.log("%cparams","font-size:16px;color:red",params)
             
                return newParamsName;
              } */
            textStyle: {
              color: "rgba(255,255,255,0)"
            }
          },
          {
            type: "inside",
            realtime: true,
            start: chartData.sta,
            end: chartData.ed,
            filterMode: "filter",
            textStyle: {
              color: "rgba(255,255,255,0)"
            }
          }
        ],
        xAxis: [
          {
            data: chartData.CurrentTime,
            position: "bottom",
            axisLine: {
              show: true,
              lineStyle: {
                color: "#acacac",
                width: 1 //这里是为了突出显示加上的
              }
            },

            axisTick: {
              show: false
            },
            axisLabel: {
              textStyle: {
                color: "#666"
              },
              formatter: function(params) {
                var newParamsName = String(params).slice(10, 16);
                /*    console.log("params")
               console.log(newParamsName) */
                return newParamsName;
              }
            }
          }
        ],
        yAxis: {
          splitLine: {
            show: true,
            lineStyle: {
              color: "#e5e5e5",
              width: 1,
              type: "solid"
            }
          },
          axisLine: {
            show: true,
            lineStyle: {
              color: "#e5e5e5",
              width: 1 //这里是为了突出显示加上的
            }
          },
          axisTick: {
            show: false
          },
          axisLabel: {
            textStyle: {
              color: "#666666"
            }
          }
        },
        series: (function() {
          /*   var dataArr = chartData.Dataarray; */
          var serie = [];
          for (var i = 0; i < chartData.echartstotaldata.length; i++) {
            var item = {
              type: "line",
              large: true,
              largeThreshold: 20000,
              animation: false,
              name: chartData.totallabel[i],
              data: chartData.echartstotaldata[i],
              connectNulls: true,
              symbol: "none"
              /*   smooth: true */
              /*  itemStyle: {
                normal: {
                  color: itemStylecolorarr[i],
                  lineStyle: {
                    color: itemStylecolorarr[i],
                    width: 2
                  }
                }
              } */
            };
            if (i == 0) {
              item.markArea = {
                silent: false,
                itemStyle: {
                  normal: {
                    color: "rgba(234,71,128,0.2)"
                  }
                },
                label: {
                  color: "rgba(234,71,128)"
                },
                data: [
                  [
                    {
                      name:
                        "预警区域:" +
                        "  " +
                        (String(chartData.Flowstarttime) == " "
                          ? "暂无数据"
                          : String(chartData.Flowstarttime).slice(0, 16)) +
                        "" +
                        "  " +
                        "—" +
                        "  " +
                        (String(chartData.Flowendtime) == " "
                          ? "暂无数据"
                          : String(chartData.Flowendtime).slice(0, 16)) +
                        "",
                      xAxis:
                        chartData.Flowendtime == " "
                          ? chartData.Flowendtime
                          : chartData.Flowstarttime
                    },
                    {
                      xAxis: chartData.Flowendtime
                    }
                  ]
                ]
              };
            }

            serie.push(item);
          }
          var seriarr = null;
          seriarr = serie;
          /* console.log("seriarr");
          console.log(seriarr); */
          return seriarr;
        })()
      });
    },
    initChart() {
      this.chart = this.$echarts.init(this.$el, "macarons");
      this.chart.clear();
      this.setOptions(this.chartData);
      /* echarts图例legend 限制最少显示一个图例,就是如何保证echarts图表里至少显示一个图例的数据（也就是最后一个图例不能变成unselected的状态）
下图是最初加载时的画面 */
      this.chart.on("legendselectchanged", function(params) {
        let option = this.getOption();
        let select_key = Object.keys(params.selected);
        let select_value = Object.values(params.selected);
        console.log(
          "select_value",
          select_value,
          "length",
          select_value.length
        );
        var n = 0;
        select_value.map(res => {
          if (!res) {
            n++;
          }
        });
        console.log("n", n);
        if (n == select_value.length) {
          option.legend[0].selected[params.name] = true;
        }

        this.setOption(option);
      });
    }
  }
};
</script>
