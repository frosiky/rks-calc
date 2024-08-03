<script setup>
import toRate from "/functions/toRate.js";
const charts = [
  { name: "譜面１", rate: 15.6, score: 99.15 },
  { name: "譜面２", rate: 15.9 },
];
const best = useCookie("best");
const add = ref({name: "", rate : "", score:""})
const add_border_color = ref({name: "gray", rate: "gray"});
const addChart = () => {
    add_border_color.value.name =(!add.value.name) ? "red" : ""
  if (!add.value.rate) {
    add_border_color.value.rate = "red"
  }
};
</script>

<template>
  <h1 style="font-size: 2.5rem">Phigros レート計算用</h1>
  <div id="charts">
    <table id="edit">
      <tr>
        <th>譜面名</th>
        <th>定数</th>
        <th>自己ベスト (%)</th>
        <th>単曲rks</th>
        <th>順位</th>
      </tr>
      <tr class="chart" v-for="chart in charts">
        <td class="data">
          {{ chart.name }}
        </td>
        <td class="data">
          {{ chart.rate }}
        </td>
        <td class="data">
          <input
            class="input"
            type="text"
            placeholder="未入力"
            :value="chart.score"
          />
        </td>
        <td class="data">
          {{
            chart.score
              ? Math.floor(toRate(chart.score / 100) * chart.rate * 100) / 100
              : "―"
          }}
        </td>
        <td class="data">{{ chart.rank ?? "―" }}</td>
      </tr>
      <tr style="height: 10px"></tr>
      <tr id="add">
        <td class="data" :style="`border: solid 2px ${add_border_color.name}`">
          <input
            id="add_name"
            class="input"
            type="text"
            :placeholder="`未入力`"
          />
        </td>
        <td class="data" :style="`border: solid 2px ${add_border_color.rate}`">
          <input id="add_rate" class="input" type="text" placeholder="未入力" />
        </td>
        <td class="data">
          <input
            id="add_score"
            class="input"
            type="text"
            placeholder="未入力"
          />
        </td>
        <td class="data">―</td>
        <td class="data hv-gr clm">追加</td>
      </tr>
    </table>
  </div>
</template>

<style scoped>
#edit {
  border-collapse: collapse;
}
.input {
  width: 100%;
  height: 3rem;
  margin: 0;
  padding: 0;
  font-size: 1rem;
  border: none;
  text-align: center;
}
.chart {
  height: 3rem;
}
.data {
  min-width: 6vw;
  text-align: center;
  border: solid 2px gray;
}

.clm {
  user-select: none;
  cursor: pointer;
}

.hv-gr:hover {
  background-color: #28952478;
  transition: background-color 0.5s;
}
</style>
