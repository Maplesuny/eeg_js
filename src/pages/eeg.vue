<template>
  <div class="echarts-box">
    <div
      id="myEcharts"
      :style="{ width: '1100px', height: 44 * channel.length +100 + 'px' }"
    ></div>
    <id
      id="testtext"
      :style="{ width: '700px', height: 40 * channel.length + 'px' }"
    >
      <!-- <q-btn label="Prompt" class="btn btn-primary" @click="prompt = true" /> -->
      <br />
      <!-- <br /> -->
      {{ ss_ing }}
      <br />
      <!-- <q-dialog v-model="prompt" persistent>
        <q-card style="min-width: 350px">
          <q-card-section>
            <div class="text-h6">Your address {{ ss_ing }}</div>
          </q-card-section>

          <q-card-section class="q-pt-none">
            <q-input autofocus @keyup.enter="prompt = false" />
          </q-card-section>

          <q-card-actions align="right" class="text-primary">
            <q-btn flat label="Cancel" v-close-popup />
            <q-btn flat label="Add address" v-close-popup />
          </q-card-actions>
        </q-card>
      </q-dialog> -->
      <br />
      <Todo
        :inputTodos="ss_ing"
        :propStartTime="select_start"
        :propEndTime="select_end"
      ></Todo>
      </id> 
  </div>
</template>
<script>
import { onMounted, ref } from "vue";
import * as echarts from "echarts";
import axios from "axios";
import json from "assets/10s.json";
// import json from "assets/10s_1channel.json";
import todo from "components/todo.vue";

export default {
  components: {
    Todo: todo,
  },
  setup() {
    // Channel Name
    const channel = [
      "Fp1-A1",
      "F3-A1",
      "C3-A1",
      "P3-A1",
      "O1-A1",
      "Fp2-A2",
      "F4-A2",
      "C4-A2",
      "F4-A2",
      "Q2-A2",
      "F7-A1",
      "T3-A1",
      "T5-A1",
      "F8-A2",
      "T4-A2",
      "T6-A2",
      "F3",
      "F4",
      "PZ",
      "Cz",
      "Fz",
      "ECG",
    ];
    let select_start = ref(0);
    let select_end = ref(0);
    let ss_ing = ref("");
    let range_start;
    let range_end;

    onMounted(() => {
      // 基於準備好的Dom,初始化echart 實例 
      const chartDom = document.getElementById("myEcharts");
      const mychart = echarts.init(chartDom);
      let start_time = 0;
      let end_time = 10;
      //   let json_url =
      //     "http://10.65.51.240:28081/api/v1/eegData?start_time=" +
      //     start_time +
      //     "&end_time=" +
      //     end_time;

      const title = [];
      const xAxis = [];
      const yAxis = [];
      const grid = [];
      const series = [];
      let res = json;

      // Count Channel number
      let count_channel = [];
      for (let i = 0; i < Object.keys(res).length; i++) {
        count_channel.push(i);
      }
      let last_channel_index = count_channel.length - 1;
      channel.forEach(function (eeg_parameter, idx) {
        if (idx != last_channel_index) {
          title.push({
            id: idx,
            textBaseline: "middle",
            top: (idx * 360) / 8.2 + 50 + "px",
            left: "1%",
            text: eeg_parameter,
          }),
            xAxis.push({
              show: false,
              // name: 'time (sec)',
              type: "category",
              boundaryGap: false,
              data: Convert_sec(512 * end_time),
              gridIndex: idx,
            }),
            yAxis.push({
              show: false,
              type: "value",
              scale: true,
              gridIndex: idx,
              // 隱藏網格線
              splitLine: {
                show: true,
              },
            });
          grid.push({
            height: "30px",
            top: (idx * 360) / 8.2 + 35 + "px",
            left: "13%",
            right: "13%",
            containLabel: false,
          });
          series.push({
            type: "line",
            data: res[idx],
            // 去掉小圓點
            symbol: "none",
            color: "#3a3c42",
            smooth: true,
            xAxisIndex: idx,
            yAxisIndex: idx,
          });
        } else {
          title.push({
            id: idx,
            textBaseline: "middle",
            top: (idx * 360) / 8.2 + 50 + "px",
            left: "1%",
            text: eeg_parameter,
            bottom: "20",
          });
          xAxis.push({
            show: true,
            name: "time (sec)",
            type: "category",
            boundaryGap: false,
            data: Convert_sec(512 * end_time),
            gridIndex: idx,
          });
          yAxis.push({
            show: false,
            type: "value",
            scale: true,
            gridIndex: idx,
            // 隱藏網格線
            splitLine: {
              show: true,
            },
          });
          grid.push({
            height: "30px",
            top: (idx * 360) / 8.2 + 35 + "px",
            left: "13%",
            right: "13%",
            containLabel: false,
          });

          series.push({
            type: "line",
            data: res[idx],
            symbol: "none",
            color: "#3a3c42",
            smooth: true,
            xAxisIndex: idx,
            yAxisIndex: idx,
          });
        }
      });

      let option = {
        // animation: false,
        title: title,
        xAxis: xAxis,
        yAxis: yAxis,
        grid: grid,
        series: series,
        dataZoom: [
          {
            type: "inside",
            xAxisIndex: count_channel,
            // zoomOnMouseWheel: 'alt'
          },
          {
            type: "slider",
            startValue: 0,
            handleIcon:
              "M10.7,11.9v-1.3H9.3v1.3c-4.9,0.3-8.8,4.4-8.8,9.4c0,5,3.9,9.1,8.8,9.4v1.3h1.3v-1.3c4.9-0.3,8.8-4.4,8.8-9.4C19.5,16.3,15.6,12.2,10.7,11.9z M13.3,24.4H6.7V23h6.6V24.4z M13.3,19.6H6.7v-1.4h6.6V19.6z",
            textStyle: {
              height: 0.5,
              color: "rgba(160, 25, 25, 1)",
            },
          },
        ],
        tooltip: {
          trigger: "axis",
        },
        toolbox: {
          // right: 5,
          bottom: "auto",
          feature: {
            dataZoom: {
              yAxisIndex: "none",
            },
            restore: {},
            saveAsImage: {},
          },
          iconStyle: {
            borderColor: "rgb(158,115,115)",
            borderWidth: 2,
            borderType: "solid",
          },
        },
        brush: {
          id: "brush",
          geoIndex: "all",
          seriesIndex: "all",
          brushLink: "all",
          toolbox: ["rect", "keep", "lineX", "clear"],
          inBrush: {
            opacity: 1,
            symbolSize: 20,
          },
          // 調整是否可平移
          transformable: false,
          throttleType: "debounce",
          throttleDelay: 600,
          //   brushMode: 'multiple',
          brushStyle: {
            borderWidth: 3,
            color: "rgba(245,39,56,0)",
            borderColor: "rgba(220,20,57,0.8)",
          },
        },
      };

      // 將數量換算成秒 ,number : 有幾筆資料
      function Convert_sec(number) {
        const dataArray = [];
        // 基底
        let base = end_time / number;
        let sum = 0;
        for (let i = 0; i < number; i++) {
          sum = sum + base;
          dataArray.push(sum);
        }
        return dataArray;
      }
      function select_brushType(params, whichLinx) {
        let brushComponent = params.batch[0];
        console.log(brushComponent);

        // 判斷X軸是LineX 還是 rect
        if (whichLinx !== "lineX") {
          range_start = brushComponent.areas[0].range[0][0];
          range_end = brushComponent.areas[0].range[0][1];
        } else {
          range_start = brushComponent.areas[0].range[0];
          range_end = brushComponent.areas[0].range[1];
        }
        console.log("range_start", range_start);
        console.log("range_end", range_end);

        // 再將rnage座標轉作為xy的座標(coordRange的值)
        let coordRange_start = mychart.convertFromPixel({ seriesIndex: 0 }, [
          range_start,
        ])[0];
        let corrdRange_end = mychart.convertFromPixel({ seriesIndex: 0 }, [
          range_end,
          ``,
        ])[0];

        console.log("coordRange_start",coordRange_start)
        console.log("corrdRange_end",corrdRange_end)

        // 因為取出的座標是點數，要除上總點數，1秒512個點，有幾秒要乘上去
        select_start.value = (coordRange_start / (512 * end_time)) * end_time;
        select_end.value = (corrdRange_end / (512 * end_time)) * end_time;

        if (select_start.value >10.0){
            alert('起始位置超出範圍')
        }else if (select_end.value >10.0){
            alert('結束位置超出範圍')
        }

        ss_ing.value =
          "Rect Range of :" +
          select_start.value +
          " second 到 " +
          select_end.value +
          " second";
        console.log("ss_ing", ss_ing.value);

        let aa =  option.title[0]
        console.log(aa)

      }

      mychart.on("brushSelected", function (params) {
        let brushComponent = params.batch[0];
        console.log('brushComponent',brushComponent)
        if (brushComponent.areas[0] !== undefined) {
          let type = brushComponent.areas[0].brushType;
          select_brushType(params, type);
        }
      });

      option && mychart.setOption(option);
    });


    return { channel, ss_ing, prompt: ref(false), select_start, select_end};
  },
};
</script>

<style scoped>
.echarts-box {
  display: flex;
}
</style>
