<script setup>
import toRate from "/functions/toRate.js";
const charts = ref([
  { name: "譜面１", rate: 15.6, score: 98.76 },
  { name: "譜面２", rate: 15.9, score: 99.68 },
  { name: "譜面３", rate: 15.9, score: 99.68 },
]);
let name_charts = charts.value.map((e) => e.name);
const best = useCookie("best");
best.value = best.value || charts.value;
const sync = () => {
  for (const chart of best.value) {
    const i = name_charts.indexOf(chart.name);
    if (i == -1) {
      charts.value.push({
        name: chart.name,
        rate: chart.rate,
        score: chart.score ?? null,
      });
    } else {
      charts.value[i] = {
        name: chart.name,
        rate: chart.rate,
        score: chart.score ?? null,
      };
    }
  }
  name_charts = charts.value.map((e) => e.name);
};
sync();
const add = ref({ name: "", rate: "", score: "" });
const add_border_color = ref({ name: "gray", rate: "gray" });
const addChart = () => {
  add_border_color.value.name = !add.value.name.length ? "red" : "gray";
  add_border_color.value.rate = !add.value.rate.length ? "red" : "gray";
  if (!(add.value.name.length && add.value.rate.length)) return;
  let suffix = "";
  if (name_charts.includes(add.value.name)) {
    for (let i = 2; true; i++) {
      if (!name_charts.includes(add.value.name + ` (${i})`)) {
        suffix = ` (${i})`;
        break;
      }
    }
  }
  best.value.push({
    name: add.value.name + suffix,
    rate: add.value.rate,
    score: add.value.score.length ? add.value.score : null,
  });
  sync();
};
const deleteChart = (index) => {
  charts.splice(index, 1)
}
</script>

<template>
  <h1 style="font-size: 2.5rem">Phigros レート計算用</h1>
  <div id="charts">
    <table id="edit">
      <tbody>
        <tr>
          <th>譜面名</th>
          <th>定数</th>
          <th>自己ベスト (%)</th>
          <th>単曲rks</th>
        </tr>
        <tr class="chart" v-for="(chart, i) in charts">
          <td class="data">
            <input
              class="input"
              type="text"
              :value="chart.name"
              @input="(event) => (best[i].name = event.target.value.length ? event.target.value : charts[i].name)"
            />
          </td>
          <td class="data">
            <input class="input" type="number" :value="chart.rate" 
             />
          </td>
          <td class="data">
            <input
              class="input"
              type="number"
              placeholder="未入力"
              :value="chart.score"
            />
          </td>
          <td class="data">
            {{
              chart.score
                ? Math.round(toRate(chart.score / 100) * chart.rate * 100) / 100
                : "―"
            }}
          </td>
          <td class="data hv-re clm" @click="deleteChart(i)">消去</td>
        </tr>
        <tr style="height: 10px"></tr>
        <tr id="add">
          <td
            class="data"
            :style="`border: solid 2px ${add_border_color.name}`"
          >
            <input
              id="add_name"
              class="input"
              type="text"
              :placeholder="`未入力`"
              @input="(event) => (add.name = event.target.value)"
            />
          </td>
          <td
            class="data"
            :style="`border: solid 2px ${add_border_color.rate}`"
          >
            <input
              id="add_rate"
              class="input"
              type="number"
              placeholder="未入力"
              @input="(event) => (add.rate = event.target.value)"
            />
          </td>
          <td class="data">
            <input
              id="add_score"
              class="input"
              type="number"
              placeholder="未入力"
              @input="(event) => (add.score = event.target.value)"
            />
          </td>
          <td class="data">
            {{
              add.score
                ? Math.round(toRate(add.score / 100) * add.rate * 100) / 100
                : "―"
            }}
          </td>
          <td class="data hv-gr clm" @click="addChart()">追加</td>
        </tr>
      </tbody>
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

.hv-re:hover {
  background-color: #95242478;
  transition: background-color 0.5s;
}
</style>
