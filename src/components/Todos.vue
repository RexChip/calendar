<template>
  <div class="Todos">
    <div v-if="currentItem === undefined" class="Todos__temp">Todo List</div>

    <div v-else class="Todos__block">
      <div class="Todos__head">{{ currentItem.title }}</div>

      <div class="Todos__input">
        <input
          type="text"
          v-model="input"
          @keyup.enter="pushTodoItem(currentItem.title)"
        />
        <div
          class="Todos__input-button"
          @click="pushTodoItem(currentItem.title)"
        >
          ADD
        </div>
      </div>

      <div class="Todos__list">
        <div
          class="Todos__item"
          :class="{ 'Todos__item--finish': item.completed }"
          v-for="(item, index) in currentItem.datas"
          :key="index"
        >
          <label>
            <div
              class="Todos__check"
              :class="{ 'Todos__check--active': item.completed }"
              @click="(item.completed = !item.completed), $forceUpdate()"
            ></div>
            <input type="checkbox" />
          </label>

          <div class="Todos__num">{{ index + 1 }}:</div>

          <div
            v-if="!item.editor"
            class="Todos__title"
            @dblclick="editorTodoItem(index), $forceUpdate()"
          >
            {{ item.title }}
          </div>

          <input
            v-else
            class="Todos__title--input"
            v-model="item.title"
            @keyup.enter="editorTodoItem(index), $forceUpdate()"
            type="text"
            v-focus
          />

          <div
            class="Todos__editor"
            :class="{ 'Todos__editor--hide': item.completed }"
            @click="editorTodoItem(index), $forceUpdate()"
          ></div>

          <div
            class="Todos__remove"
            @click="removeTodoItem(index), $forceUpdate()"
          ></div>
        </div>
      </div>
    </div>

    <div class="Todos__note">
      <div>!</div>
      <div>標題點擊兩下 可以直接編輯。</div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["propDate", "propItem"],
  data() {
    return {
      input: "",
      titleInput: "",
    };
  },
  computed: {
    currentDate() {
      return this.propDate;
    },
    currentItem() {
      return this.propItem;
    },
  },
  methods: {
    pushTodoItem() {
      const self = this;

      if (self.input === "") return false;

      self.currentItem.datas.push({
        title: self.input,
        completed: false,
        editor: false,
      });

      self.input = "";

      this.$emit("update", self.currentDate.split("-"));
    },
    editorTodoItem(index) {
      this.currentItem.datas[index].editor = !this.currentItem.datas[index]
        .editor;
    },
    removeTodoItem(index) {
      const self = this;

      self.currentItem.datas.splice(index, 1);

      if (this.currentItem.datas.length == 0) {
        this.$emit("remove", self.currentDate.split("-"));
      }
      // this.$set(this.currentItem.datas, 0, this.currentItem.datas);
    },
  },
  // 在 component 註冊局部自定義指令(register a directive locally)，使用 directives 選項
  directives: {
    focus: {
      // 當被綁定的 element 插入到 dom 時執行
      inserted: function (el) {
        el.focus();
      },
    },
  },
};
</script>

<style lang="scss">
$mc: #f34b7f;
$sc: #3c4757;

.Todos {
  position: relative;

  display: flex;
  justify-content: center;

  width: 100%;

  background-color: $sc;
  box-shadow: 0 0 5px rgba(#000, 0.15);

  color: $mc;

  padding: {
    top: 60px;
    left: 3%;
    right: 3%;
    bottom: 60px;
  }

  @media (min-width: 781px) {
    position: fixed;
    top: 0;
    left: 0;

    width: 35%;
    height: 100vh;
  }

  @media (max-width: 780px) {
    flex-grow: 1;

    overflow-y: auto;

    padding: {
      top: 30px;
      bottom: 30px;
    }
  }

  &__temp {
    font-size: 24px;
    font-weight: 500;
    font-family: "Lora", serif;
    letter-spacing: 0.07em;

    text-align: center;
  }

  &__head {
    font-size: 24px;
    font-weight: 600;
    letter-spacing: 0.07em;

    text-align: center;

    @media (max-width: 780px) {
      font-size: 20px;
    }
  }

  &__input {
    display: flex;
    align-items: center;

    padding: {
      top: 30px;
    }

    input {
      // flex-grow: 1;
      width: 100%;

      font-size: 16px;
      line-height: 20px;
      letter-spacing: 0.07em;
      color: $mc;

      background-color: $sc;
      outline: none;

      border: {
        top: solid 2px $mc;
        left: solid 2px $mc;
        right: solid 2px $mc;
        bottom: solid 2px $mc;
        top-left-radius: 3px;
        bottom-left-radius: 3px;
      }

      padding: {
        top: 5px;
        left: 10px;
        right: 10px;
        bottom: 5px;
      }
    }

    &-button {
      font-weight: bold;
      line-height: 30px;

      transition: all 0.3s;
      cursor: pointer;

      border: {
        top: solid 2px $mc;
        right: solid 2px $mc;
        bottom: solid 2px $mc;
        top-right-radius: 3px;
        bottom-right-radius: 3px;
      }

      padding: {
        left: 15px;
        right: 15px;
      }

      &:hover {
        background-color: $mc;
        color: $sc;
      }
    }
  }

  &__block {
    width: 100%;
    max-width: 380px;
  }

  &__list {
    padding: {
      top: 30px;
    }
  }

  &__item {
    position: relative;

    display: flex;
    align-items: center;
    justify-content: space-between;

    gap: 10px;

    &:nth-of-type(n + 2) {
      margin: {
        top: 15px;
      }
    }

    &::before {
      content: "";
      position: absolute;
      top: 50%;
      left: 30px;

      display: block;
      width: calc(100% - 70px);
      height: 2px;

      background-color: $mc;

      transform: scaleX(0);
      transform-origin: right;
      transition: transform 0.3s;

      margin: {
        top: -1px;
      }
    }

    > div:first-child {
      font-size: 20px;
      font-weight: 500;
    }

    &--finish {
      &::before {
        transform: scaleX(1);
        transform-origin: left;
      }

      .Todos__title {
        pointer-events: none;
      }
    }
  }

  &__check {
    display: flex;
    align-items: center;
    justify-content: center;

    width: 20px;
    height: 20px;

    border: solid 2px $mc;

    transition: opacity 0.3s;

    cursor: pointer;

    &:hover {
      opacity: 0.5;
    }

    &::before {
      content: "";
      width: 12px;
      height: 12px;

      background-color: $mc;

      transform: scale(0);
      transition: transform 0.3s;
    }

    &--active {
      &::before {
        transform: scale(1);
      }
    }

    & + input {
      display: none;
    }
  }

  &__num {
    font-size: 20px;
  }

  &__title {
    flex-grow: 1;

    font-size: 20px;
    font-weight: 500;
    letter-spacing: 0.05em;
    line-height: 30px;

    border: {
      bottom: solid 2px transparent;
    }

    &--input {
      width: 100%;

      font-size: 18px;
      font-weight: 400;
      letter-spacing: 0.05em;
      line-height: 30px;
      color: #fff;

      outline: none;

      background-color: transparent;

      border: {
        top: initial;
        left: initial;
        right: initial;
        bottom: solid 2px $mc;
      }

      padding: 0;
    }
  }

  &__editor {
    flex-shrink: 0;

    width: 30px;
    height: 30px;

    background-size: 20px;
    background-position: center;
    background-repeat: no-repeat;
    background-image: url("../images/edit.svg");

    transition: all 0.3s;

    cursor: pointer;

    &:hover {
      opacity: 0.5;
    }

    &--hide {
      pointer-events: none;
      transform: scale(0);
    }
  }

  &__remove {
    flex-shrink: 0;

    width: 30px;
    height: 30px;

    background-size: 30px;
    background-position: center;
    background-repeat: no-repeat;
    background-image: url("../images/review.svg");

    transition: all 0.3s;

    cursor: pointer;

    &:hover {
      opacity: 0.5;
    }
  }

  &__note {
    position: absolute;
    top: 20px;
    left: 20px;

    div {
      &:nth-of-type(1) {
        display: flex;
        align-items: center;
        justify-content: center;

        width: 30px;
        height: 30px;

        font-size: 22px;
        font-weight: bold;

        border-radius: 50%;
        border: solid 2px $mc;

        &:hover + div {
          transform: translateY(0);
          opacity: 1;
        }
      }

      &:nth-of-type(2) {
        position: absolute;
        top: calc(100% + 10px);
        left: 0;

        font-size: 14px;
        letter-spacing: 0.05em;
        line-height: 1.7;
        color: $sc;
        word-break: keep-all;

        background-color: $mc;

        pointer-events: none;

        transition: all 0.3s;
        transform: translateY(-10px);
        opacity: 0;

        padding: {
          top: 5px;
          left: 10px;
          right: 10px;
          bottom: 5px;
        }
      }
    }
  }
}
</style>
