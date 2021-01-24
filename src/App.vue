<template>
  <div id="app">
    <Todos
      :prop-date="changeDate"
      :prop-item="changeItem"
      @update="updateSign"
      @remove="removeSign"
    />
    <Calendar :check-date="checkDate" @update="changeTodo" />
  </div>
</template>

<script>
import Calendar from "./components/Calendar.vue";
import Todos from "./components/Todos.vue";

export default {
  name: "App",
  components: {
    Calendar,
    Todos,
  },
  data() {
    return {
      checkDate: {},
      currentDate: "",
      datas: {
        // "2021-1-1": {
        //   title: "Dec-12-2020",
        //   datas: [
        //     {
        //       title: "Test (1)",
        //       completed: true,
        //     },
        //     {
        //       title: "Test (2)",
        //       completed: false,
        //     },
        //   ],
        // },
        // better data
        // "2021": [
        //   {
        //     month: "Jan",
        //     day: {
        //       "1": [
        //         {
        //           title: "Todos (1)",
        //           completed: false,
        //         },
        //       ],
        //       "2": [
        //         {
        //           title: "Todos (2)",
        //           completed: false,
        //         },
        //       ],
        //     },
        //   },
        // ],
      },
    };
  },
  computed: {
    changeDate() {
      return this.currentDate;
    },
    changeItem() {
      return this.datas[this.currentDate];
    },
  },
  methods: {
    changeTodo(item) {
      if (item === undefined) item = "";

      this.currentDate = item;

      if (this.datas[item] === undefined) {
        this.datas[item] = this.presetData(item);
      }
    },
    presetData(el) {
      return {
        title: el,
        datas: [],
      };
    },
    updateSign(item) {
      let month = item.slice(0, 2).join("-");
      let day = item[2];

      if (this.checkDate[month] === undefined) {
        // 使用 $set 新增 array，讓資料即時更新
        this.$set(this.checkDate, month, []);
      }

      if (this.checkDate[month].indexOf(day) === -1) {
        this.checkDate[month].push(day);
      }
    },
    removeSign(item) {
      let month = item.slice(0, 2).join("-");
      let day = item[2];
      let index = this.checkDate[month].indexOf(day);

      this.checkDate[month].splice(index, 1);
    },
  },
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=PT+Sans:wght@400;700&display=swap");
@import url("https://fonts.googleapis.com/css2?family=Lora:wght@400;500&family=Noto+Sans+TC:wght@400;500&display=swap");

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;

  &::before,
  &::after {
    box-sizing: border-box;
  }
}

#app {
  font-family: "PT Sans", "Noto Sans TC", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  letter-spacing: 0.05em;
  color: #2c3e50;

  user-select: none;

  @media (min-width: 781px) {
    padding: {
      top: 60px;
      left: 35%;
    }
  }

  @media (max-width: 780px) {
    display: flex;
    flex-direction: column;

    height: 100vh;
  }
}
</style>
