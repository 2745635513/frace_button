<template>
  <div class="hello">
    <frace-button :btns="btns">
      <template v-slot:aaa>
        <el-button>aaa</el-button>
      </template>
    </frace-button>
  </div>
</template>

<script>
import FraceButton from "../../packages/button/button";
export default {
  name: "HelloWorld",
  components: { FraceButton },
  props: {
    msg: String,
  },
  data() {
    return {
      btns: [],
    };
  },
  mounted() {
    this.btns = [
      {
        code: "b_nice",
        label: "nice",
      },
      {
        code: "b_hello",
        label: "hello",
      },
      {
        code: "aaa",
      },
      {
        code: "save",
      },
      {
        code: "b_ok",
        label: "ok",
      },
      {
        code: "b_no",
        label: "no",
      },
      {
        code: "b_hi",
        label: "hi",
      },
    ];
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
