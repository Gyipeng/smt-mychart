<template>
  <div class="myChart" style="width: 300px;height: 300px">
    {{name}}
  </div>
</template>

<script>

  export default {
    name: "my-chart",
    data() {
      return {}
    },
    mounted() {
      this.actions.onGlobalStateChange((newState, prev) => {
        this.drawLine(newState);
      }, true)
    },
    methods: {
      drawLine(state) {
        let dom = document.querySelector(`.${this.className}`)
        let myChart = window.echarts.init(dom)
        if(!myChart.isClick){
          myChart.on("click", (item) => {
            this.actions.setGlobalState({
              [this.keys]: {
                condition:{
                  category: item.dataIndex,
                },
                range:this.range
              },

            })
          })
          myChart.isClick=true
        }
        let option = this.getOption()
        option = this.filterOption(option, state)
        // 绘制图表
        myChart.setOption(option);
      },
      filterOption(option, state) {
        for (let key in state) {
          if (state[key].range.includes(this.keys)) {
            if (key === "system") {
              //todo  option 覆盖
              option.series[0].itemStyle = Object.assign({}, option.series[0].itemStyle, state[key].itemStyle)
            }
          }
        }
        return option
      },
      getOption() {
        let {data} = this.options;
        let xData = data.map(item => item.name)
        let yData = data.map(item => item.value)
        let option = {
          title: {text: this.keys},
          tooltip: {},
          xAxis: {
            data: xData
          },
          yAxis: {},
          series: [{
            name: '销量',
            type: 'bar',
            data: yData,
            itemStyle: {
              color: "green"
            }
          }]
        }
        return option;
      }
    },
    props: {
      className: {
        type: String,
        default: "myChart"
      },
      options: {
        type: Object,
        default: () => {
        }
      },
      keys: String,
      actions: Object,
      range:Array
    },

  }
</script>
