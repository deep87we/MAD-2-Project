<template lang="">
  <div class="single-task">
    <Bar
      v-if="loaded"
      id="bar"
      :chart-options="chartOptions"
      :chart-data="chartData"
      :chart-id="chartId"
      :dataset-id-key="datasetIdKey"
      :plugins="plugins"
      :css-classes="cssClasses"
      :styles="styles"
      :width="width"
      :height="height"
    />
    <button class="button" @click="exportToPDF(0)">
      {{ download_status }}
    </button>
    <table class="min-w-full table-auto">
      <thead>
        <tr class="table-row">
          <th class="table-row-header">
            <span>Value</span>
          </th>
          <th class="table-row-header">
            <span>Notes</span>
          </th>
          <th class="table-row-header">
            <span>Date</span>
          </th>
          <th class="table-row-header">
            <span>Delete</span>
          </th>
        </tr>
      </thead>
      <tbody v-for="taskLog in tasklogs" :key="taskLog.id" class="table-body">
        <tr class="table-body-row">
          <td class="table-row-header">
            <span>{{ taskLog.value }}</span>
          </td>
          <td class="table-row-header">
            <span>{{ taskLog.notes }}</span>
          </td>

          <td class="table-row-header">
            <span class="bg-green-500 text-white px-5 py-1 rounded-full">{{
              taskLog.date
            }}</span>
          </td>
          <td class="table-row-header">
            <button :id="taskLog.id" @click="(e) => deleteLog(e)">
              Delete
            </button>
          </td>
        </tr>
      </tbody>
    </table>
    <button @click="toggleLog()" class="cursor">Add Log</button>
    <!-- add log -->
    <div v-show="modallog" class="modal-mask">
      <div class="modal-header">
        <slot name="header"> Add Log </slot>
      </div>

      <div class="modal-body">
        <div>
          <label class="txt-field">Date</label>
          <input v-model="logData.date" type="date" required />
        </div>
        <div>
          <label class="txt-field">Value</label>
          <input v-model="logData.value" type="text" required />
        </div>
        <div>
          <label class="txt-field">Notes</label>
          <input v-model="logData.notes" type="text" required />
        </div>
      </div>

      <div class="modal-button">
        <button @click="[toggleLog(), LogBtn()]" class="modal-default-button">
          Submit
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { Line as Bar } from "vue-chartjs/legacy";
import Exporter from "vue-chartjs-exporter";
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Tooltip,
  Legend,
} from "chart.js";
import axios from "axios";
ChartJS.register(
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Tooltip,
  Legend
);

export default {
  name: "Task",
  components: { Bar },
  props: {
    chartId: {
      type: String,
      default: "bar-chart",
    },
    datasetIdKey: {
      type: String,
      default: "label",
    },
    width: {
      type: Number,
      default: 700,
    },
    height: {
      type: Number,
      default: 400,
    },
    cssClasses: {
      default: "",
      type: String,
    },
    styles: {
      type: Object,
      default: () => {},
    },
    plugins: {
      type: Object,
      default: () => {},
    },
  },
  data() {
    return {
      loaded: false,
      modallog: false,
      tasklogs: [],
      chartDate: [],
      chartValue: [],
      logData: {
        date: "",
        value: "",
        notes: "",
        task_id: this.$route.params.taskid,
      },
      download_status: "Download Charts",
      log: [],
      chartData: {
        label: "My Log Chart",
        labels: ["2022-08-02", "2022-08-04"],
        datasets: [{ label: "My Log Chart", data: this.chartValue }],
      },
      chartOptions: {
        maintainAspectRatio: true,
        responsive: true,
        options: {
          scales: {
            yAxes: [
              {
                ticks: {
                  beginAtZero: true,
                },
                gridLines: {
                  display: true,
                },
              },
            ],
            xAxes: [
              {
                gridLines: {
                  display: false,
                },
              },
            ],
          },
          legend: {
            display: false,
          },
          responsive: true,
          maintainAspectRatio: false,
        },
      },
    };
  },
  async created() {
    this.get();
  },

  methods: {
    exportToPDF() {
      this.download_status = "Processing...";
      let bar = document.getElementById("bar");

      const exp = new Exporter([bar]);
      exp.export_pdf().then((pdf) => {
        pdf.save("charts.pdf");
        this.download_status = "Download Charts";
      });
    },
    async get() {
      const id = this.$route.params.taskid;
      const token = localStorage.getItem("token");
      try {
        const res = await axios.get(`http://127.0.0.1:5000/api/log/${id}`, {
          headers: { authorization: "Bearer " + `${token}` },
        });
        console.log(res.data.data);
        this.chartDate = res.data.data.map((items) => items.date);
        this.chartValue = res.data.data.map((items) => items.value);
        this.tasklogs = res.data.data;
        this.loaded = true;
      } catch (error) {
        console.log(error);
      }
    },
    async deleteLog(event) {
      try {
        const token = localStorage.getItem("token");
        const logid = event.target.id;
        console.log(logid);
        const reposnse = await axios.delete(
          `http://127.0.0.1:5000/api/log/${logid}`,
          {
            headers: { authorization: "Bearer " + `${token}` },
          }
        );
        this.get();
      } catch (error) {
        console.log(error);
      }
    },
    toggleLog() {
      this.modallog = !this.modallog;
    },
    async LogBtn() {
      const token = localStorage.getItem("token");

      await axios.post("http://127.0.0.1:5000/api/log", this.logData, {
        headers: { authorization: "Bearer " + `${token}` },
      });
      this.get();
    },
  },
};
</script>

<style scoped>
.single-task {
  margin-top: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}
.table-auto {
  table-layout: auto;
  margin-top: 40px;
}
p {
  font-weight: bold;
}
.table-row {
  background: #818cf8;
  color: white;
}
.table-row-header {
  padding: 8px 64px;
}
.table-body {
  background: #e5e7eb;
}
.table-body-row {
  background: #f9fafb;
  text-align: center;
}
.table-row-header a {
  text-decoration: none;
}
.table-header {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
  align-items: center;
}
button {
  margin-top: 10px;
  color: white;
  background: #22c55e;
  font-weight: 500;
  border-radius: 8px;
  font-size: 16px;
  padding: 10px 16px;
  outline: none;
  border: 1px solid;
}
button:hover {
  color: white;
  background: #16a34a;
}
.cursor {
  background: #be123c;
  margin-top: 20px;
}
.cursor:hover {
  background: #e11d48;
}
.modal-mask {
  display: flex;
  flex-direction: column;
  background: white;
  justify-content: center;
  align-items: center;
  z-index: 99;
  width: 440px;
  position: relative;
  bottom: 220px;
  left: 10px;
  box-shadow: 10px 10px 15px 10px rgba(0, 0, 0, 0.05);
  padding: 20px;
  border-radius: 6px;
}
.modal-button {
  padding: 10px 60px;
}
.modal-header {
  color: #154018;
  font-weight: bold;
  font-size: 20px;
  margin-bottom: 10px;
  border-bottom: 1px solid rgb(43, 39, 39);
  width: 100%;
  text-align: center;
}
input {
  background-color: #f9fafb;
  border: 1px solid gray;
  color: black;
  font-size: 18px;
  border-radius: 12px;
  width: 100%;
  padding: 10px;
  margin-bottom: 8px;
  outline: none;
}
input:focus {
  border: 2px solid blue;
}
</style>
