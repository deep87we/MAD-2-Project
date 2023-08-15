<template>
  <div>
    <div class="home">
      <taskTable />
    </div>
  </div>
</template>

<script>
import taskTable from "../components/Table.vue";
export default {
  name: "Home",
  components: { taskTable },
};
</script>
<style>
.home {
  margin-top: 40px;

  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
