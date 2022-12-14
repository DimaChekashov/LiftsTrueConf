<template>
  <elevator />
</template>

<script lang="ts">
import { defineComponent } from "vue";
import Elevator from "./components/Elevator.vue";

export default defineComponent({
  name: "App",
  components: { Elevator },
});
</script>

<style lang="scss">
* {
  box-sizing: border-box;
  padding: 0;
  margin: 0;
}

#app {
  font-family: Arial, Helvetica, sans-serif;
  padding: 15px;
  height: 100vh;
}
</style>
