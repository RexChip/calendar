<template>
  <div class="Calendar">
    <div class="Calendar__month">
      <div class="Calendar__month--prev" @click="newMonth(-1)">Prev</div>
      <div>{{ monthArray[date.month] }} {{ date.year }}</div>
      <div class="Calendar__month--next" @click="newMonth(1)">Next</div>
    </div>

    <div class="Calendar__week">
      <div
        class="Calendar__week-item"
        v-for="(item, index) in week"
        :key="index"
      >
        {{ item }}
      </div>
    </div>

    <div class="Calendar__date">
      <div
        class="Calendar__item Calendar__item--fake"
        v-for="item in fakeLength"
        :key="'fake' + item"
      ></div>

      <div
        class="Calendar__item"
        :class="checkItem(item)"
        v-for="item in date.all"
        @click="changeItem(item)"
        :key="returnDate(item)"
      >
        {{ item }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["checkDate"],
  data() {
    return {
      fakeLength: 0,
      week: ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"],
      monthArray: [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December",
      ],
      date: {
        year: 0,
        month: 0,
        strMonth: "",
        first: "",
        all: 0,
      },
    };
  },
  methods: {
    checkItem(item) {
      let temp = [this.date.year, this.date.month + 1].join("-");

      if (this.checkDate[temp] === undefined) return "";

      if (this.checkDate[temp].indexOf(String(item)) !== -1) {
        return "Calendar__item--active";
      }
    },
    returnDate(item) {
      return [
        String(this.date.year),
        String(this.date.month + 1),
        String(item),
      ].join("-");
    },
    changeItem(item) {
      let temp = this.returnDate(item);

      this.$emit("update", temp);
    },
    newMonth(el) {
      let year = this.date.year;
      let month = this.date.month + el;
      let newDate = new Date(year, month);
      let strMonth = newDate.toDateString().split(" ")[1];
      let first = String(new Date(year, month));
      let all = Number(
        new Date(year, month + 1, 0).toDateString().split(" ")[2]
      );

      if (month === -1) {
        year -= 1;
        month = 11;
      } else if (month === 12) {
        year += 1;
        month = 0;
      }

      this.fakeLength = this.week.indexOf(first.split(" ")[0]);

      this.date = {
        year,
        month,
        strMonth,
        first,
        all,
      };
    },
  },
  mounted() {
    let date = new Date();
    let year = date.getFullYear();
    let month = date.getMonth();
    let strMonth = date.toDateString().split(" ")[1];
    let first = String(new Date(year, date.getMonth()));
    let all = Number(
      new Date(year, date.getMonth() + 1, 0).toDateString().split(" ")[2]
    );

    this.fakeLength = this.week.indexOf(first.split(" ")[0]);

    this.date = {
      year,
      month,
      strMonth,
      first,
      all,
    };

    // this.datas[year][month] = {
    //   month: strMonth,
    //   day: {},
    // };
  },
};
</script>

<style lang="scss">
$mc: #f34b7f;
$sc: #3c4757;

.Calendar {
  display: flex;
  flex-direction: column;

  width: 100%;
  max-width: 1140px;

  margin: {
    left: auto;
    right: auto;
  }

  padding: {
    left: 3%;
    right: 3%;
  }

  @media (max-width: 780px) {
    padding: {
      top: 20px;
      bottom: 20px;
    }
  }

  &__month {
    display: flex;
    align-items: center;
    justify-content: space-between;

    font-size: 22px;
    font-weight: 600;
    text-align: center;

    padding: {
      bottom: 30px;
    }

    @media (max-width: 780px) {
      font-size: 18px;

      padding: {
        bottom: 10px;
      }
    }

    &--prev,
    &--next {
      cursor: pointer;

      width: calc(100% / 7);

      transition: color 0.3s;

      &:hover {
        color: $mc;
      }
    }
  }

  &__week {
    display: flex;
    flex-wrap: wrap;
    width: 100%;

    &-item {
      display: flex;
      align-items: center;
      justify-content: center;

      width: calc(100% / 7);
      height: 60px;

      font-family: "PT Sans", sans-serif;
      font-size: 20px;
      font-weight: bold;
      letter-spacing: 0.05em;
      color: $sc;

      @media (max-width: 780px) {
        height: 45px;
        font-size: 16px;
      }
    }
  }

  &__date {
    display: flex;
    flex-wrap: wrap;
    width: 100%;
  }

  &__item {
    display: flex;
    align-items: center;
    justify-content: center;

    width: calc(100% / 7);
    height: 60px;

    font-family: "PT Sans", sans-serif;
    font-size: 20px;
    font-weight: bold;
    letter-spacing: 0.05em;
    color: $sc;

    transition: all 0.3s ease-in-out;
    cursor: pointer;

    &:hover {
      background-color: $sc;
      color: $mc;
    }

    @media (max-width: 780px) {
      height: 45px;
      font-size: 16px;
    }

    &--active {
      position: relative;

      &:hover::before {
        background-color: $mc;
      }

      &::before {
        content: "";
        position: absolute;
        bottom: 10px;
        left: 50%;

        display: block;
        width: 5px;
        height: 5px;

        border-radius: 50%;
        background-color: $sc;

        transition: all 0.3s;

        margin: {
          left: -2.5px;
        }

        @media (max-width: 780px) {
          bottom: 5px;
        }
      }
    }

    &--fake {
      pointer-events: none;
      visibility: hidden;
      opacity: 0;
    }
  }
}
</style>
