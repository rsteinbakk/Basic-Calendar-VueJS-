<template>
  <div id="app">
    <BasicCalendar />
    <div class="container">
      <p>
        <b>Basic Calendar v. 1.0</b><br />Simple calendar written in JavaScript,
        HTML and CSS (Vue JS framework, CLI). Ready to communicate with database
        (fetch in this example).
      </p>
      <div>
        See code at&nbsp;
        <a href="https://github.com/rsteinbakk/Basic-Calendar-VueJS-">Github</a
        >&nbsp; or visit my website&nbsp;
        <a href="www.rogersteinbakk.no">www.rogersteinbakk.no</a>
      </div>
    </div>
  </div>
</template>

<script>
import BasicCalendar from "./components/BasicCalendar.vue";

export default {
  name: "App",
  components: {
    BasicCalendar,
  },
};
</script>
<style scoped>
/* Basic Calendar */

* {
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
}
a {
  color: unset;
  color: rgb(82, 82, 82);
  text-decoration-color: rgb(158, 158, 158);
}
.container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  text-align: center;
  font-size: 14px;
}
</style>
