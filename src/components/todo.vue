<template>
  <div id="App1">
    <input type="text" placeholder="請輸入Channel" v-model="inputTodo" />
    <button v-on:click="Add">Add</button>
    <hr />
    <ol>
      <li v-for="(item, idx) in todos" :key="idx">
        <input type="checkbox" v-model="item.finish" />
        <input
          type="text"
          v-bind:disabled="item.finish"
          v-model="item.channel"
        />
        <button v-if="item.finish" v-on:click="Del(idx)">X</button>
      </li>
    </ol>
    <hr />
    {{ todos }}
    <br />
    <br />
    <q-btn color="primary" @click="ssaveFile( todos)">Savedata</q-btn>
  </div>
</template>

<script>
export default {
  name: "todo.vue",
  props: {
    inputTodos: String,
    channel: Number || String,
    propStartTime: Number,
    propEndTime: Number,
  },
  data() {
    // let inputTodo = this.props.inputTodos
    // console.log('this.props.inputTodos: ', inputTodos)
    return {
      inputTodo: "",
      todos: [
        { channel: "1", StartTime: 0, EndTime: 0, finish: false },
        { channel: "2", StartTime: 0, EndTime: 0, finish: false },
        { channel: "3", StartTime: 0, EndTime: 0, finish: false },
      ],
    };
  },
  // mounted() {
  //   // eslint-disable-next-line @typescript-eslint/no-unsafe-assignment
  //   this.inputTodo = this.inputTodos
  // },
  watch: {
    inputTodos() {
      this.inputTodo = this.inputTodos;
    },
  },
  methods: {
    Add: function () {
      let tmpTodo = {};

      tmpTodo.channel = this.inputTodo;
      tmpTodo.StartTime = this.propStartTime.toFixed(2);
      tmpTodo.EndTime = this.propEndTime.toFixed(2);
      this.todos.push(tmpTodo);
      this.inputTodo = "";
    },
    Del: function (idx) {
      console.log(idx);
      this.todos.splice(idx, 1);
    },

    saveFile: function (DDate) {
      const data = JSON.stringify(DDate);
      window.localStorage.setItem("arr", data);
      console.log(JSON.parse(localStorage.getItem("arr")));
    },

    ssaveFile: function(DDate) {
    const data = JSON.stringify(DDate)
    const blob = new Blob([data], {type: 'text/plain'})
    const e = document.createEvent('MouseEvents'),
    a = document.createElement('a');
    a.download = "test.json";
    a.href = window.URL.createObjectURL(blob);
    a.dataset.downloadurl = ['text/json', a.download, a.href].join(':');
    e.initEvent('click', true, false, window, 0, 0, 0, 0, 0, false, false, false, false, 0, null);
    a.dispatchEvent(e);
}
   
  },
};
</script>

<style scoped></style>
