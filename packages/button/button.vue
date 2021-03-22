<template>
  <div>
    <template v-for="item in cmBtnList" :key="item.code">
      <slot v-if="item.add" :name="item.code"></slot>
      <el-button
        v-else
        @click.stop.prevent="handleClick(item.code)"
        :icon="item.icon"
        >{{ item.label }}</el-button
      >
    </template>
    <component @close="close" :is="currentTabComponent"></component>
  </div>
</template>
<script>
import PassModal from "./modal/pass_modal";
import RejectModal from "./modal/reject_modal";
import { BACK_BUTTONS, SUBMIT_BUTTON, SAVE_BUTTON } from "@/constants/button";
import { find } from "lodash";
export default {
  name: "FraceButton",
  components: { PassModal, RejectModal },
  props: {
    btns: Array,
  },
  data() {
    return {
      btnList: [],
      currentTab: null,
    };
  },
  computed: {
    currentTabComponent() {
      if (this.currentTab !== null) {
        let str = this.currentTab.substr(0, 1).toUpperCase();
        return str + this.currentTab.substr(1) + "Modal";
      } else {
        return null;
      }
    },
    cmBtnList() {
      let btnList = [];
      if (this.btns.length > 0) {
        this.btns.forEach((item) => {
          let obj = find(BACK_BUTTONS, { code: item.code });
          if (obj) {
            //判断后台已配置
            btnList.push(obj);
          } else if (item.code === SAVE_BUTTON.code) {
            btnList.push(SAVE_BUTTON);
          } else {
            btnList.push({
              code: item.code,
              add: true,
            });
          }
        });
      }
      if (this.$route.query.type === "1") {
        btnList = btnList.filter((res) => {
          return !res.code.startsWith("b_");
        });
        if (!find(btnList, { code: SUBMIT_BUTTON.code })) {
          btnList.push(SUBMIT_BUTTON);
        }
        if (!find(btnList, { code: SAVE_BUTTON.code })) {
          btnList.push(SAVE_BUTTON);
        }
      }
      return btnList;
    },
  },
  methods: {
    handleClick(code) {
      this.currentTab = code;
    },
    close() {
      this.currentTab = null;
    },
    show() {
      this.btnList = [];
      if (this.btns.length > 0) {
        this.btns.forEach((item) => {
          let obj = find(BACK_BUTTONS, { code: item.code });
          if (obj) {
            //判断后台已配置
            this.btnList.push(obj);
          } else {
            this.btnList.push({
              code: item.code,
              add: true,
            });
          }
        });
      }
    },
  },
};
</script>
