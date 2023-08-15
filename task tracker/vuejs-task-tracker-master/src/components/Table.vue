<template>
  <div>
    <p v-show="!userlogged" class="">
      You are not logged in please login and craete your task
    </p>
    <div v-show="userlogged" class="table-header">
      <button @click="logfn()">logout</button>
      <div>
        <p>Hello {{ userShow }}</p>
      </div>
      <div><button @click="toggleNav()">Add Task</button></div>
    </div>
    <table v-show="userlogged" class="min-w-full table-auto">
      <thead>
        <tr class="table-row">
          <th class="table-row-header">
            <span>Name</span>
          </th>
          <th class="table-row-header">
            <span>Description</span>
          </th>
          <th class="table-row-header">
            <span>Add Log</span>
          </th>
          <th class="table-row-header">
            <span>Delete Task</span>
          </th>
        </tr>
      </thead>
      <tbody class="table-body">
        <tr v-for="task in tasks" :key="task.id" class="table-body-row">
          <td class="table-row-header">
            <router-link :to="{ name: 'task', params: { taskid: task.id } }">{{
              task.name
            }}</router-link>
          </td>
          <td class="table-row-header">
            <span>{{ task.description }}</span>
          </td>

          <td class="table-row-header">
            <span>{{ task.date }}</span>
          </td>
          <td class="table-row-header">
            <button :id="task.id" @click="(e) => deleteTask(e)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>
    <!-- add task -->
    <div v-show="modalshow" class="modal-mask">
      <div class="modal-header">
        <slot name="header"> Add Task </slot>
      </div>

      <div class="modal-body">
        <div>
          <label class="txt-field">Name</label>
          <input v-model="formData.name" type="text" required />
        </div>
        <div>
          <label class="txt-field">Description</label>
          <input v-model="formData.description" type="text" required />
        </div>
        <div>
          <label class="txt-field">Date</label>
          <input v-model="formData.date" type="date" required />
        </div>
      </div>

      <div class="modal-button">
        <button @click="[toggleNav(), TaskBtn()]" class="modal-default-button">
          Submit
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import "vue-good-table/dist/vue-good-table.css";
import axios from "axios";
import { VueGoodTable } from "vue-good-table";
export default {
  name: "taskTable",
  components: {
    VueGoodTable,
  },
  data() {
    return {
      tasks: [],
      userlogged: true,
      userShow: "",
      modalshow: false,
      logOut: false,
      formData: {
        name: "",
        description: "",
        date: "",
      },
    };
  },
  created() {
    this.GetTask();
  },
  methods: {
    async TaskBtn() {
      const token = localStorage.getItem("token");

      const res = await axios.post(
        "http://127.0.0.1:5000/api/task",
        this.formData,
        {
          headers: { authorization: "Bearer " + `${token}` },
        }
      );
      const data = res.data.data;
      console.log(data);
      this.GetTask();
    },

    async GetTask() {
      try {
        const token = localStorage.getItem("token");
        const reposnse = await axios.get("http://127.0.0.1:5000/api/task", {
          headers: { authorization: "Bearer " + `${token}` },
        });
        this.tasks = reposnse.data.data;
        this.userlogged = true;
        this.userShow = localStorage.getItem("username");
      } catch (error) {
        console.log(error);
        this.userlogged = false;
        console.log(this.userlogged);
      }
    },
    async deleteTask(event) {
      try {
        const token = localStorage.getItem("token");
        const id = event.target.id;
        const reposnse = await axios.delete(
          `http://127.0.0.1:5000/api/task/${id}`,
          {
            headers: { authorization: "Bearer " + `${token}` },
          }
        );
        console.log(reposnse.data);
        this.GetTask();
      } catch (error) {
        console.log(error);
      }
    },
    logfn() {
      this.logOut = true;
      localStorage.removeItem("username");
      localStorage.removeItem("token");
      this.GetTask();
    },

    toggleNav() {
      this.modalshow = !this.modalshow;
    },
  },
};
</script>
<style scoped>
.table-auto {
  table-layout: auto;
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
.user-name {
  color: #154018;
}
button {
  color: white;
  background: #22c55e;
  font-weight: 500;
  border-radius: 8px;
  font-size: 16px;
  padding: 10px 20px;
  outline: none;
  border: 1px solid;
}
button:hover {
  color: white;
  background: #16a34a;
}
.cursor {
  background: #be123c;
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
  bottom: 80px;
  left: 120px;
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
.txt-field {
  margin-top: 4px;
  text-align: center;
  display: flex;
  justify-content: center;
  color: #0a178d;
  margin-bottom: 4px;
  font-weight: bold;
}
</style>
