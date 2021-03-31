<template>
  <div ref="buttons" class="container">
    <div class="visible-button" :style="{ width: visibleWidth + 'px' }">
      <template v-for="item in cmBtnList" :key="item.code">
        <slot :ref="item.code" v-if="item.add" :name="item.code"></slot>
        <el-button
          v-else
          :ref="item.code"
          @click.stop.prevent="handleClick(item.code)"
          :icon="item.icon"
          >{{ item.label }}</el-button
        >
      </template>
    </div>

    <el-dropdown
      style="margin-left:10px"
      v-if="cmBtnList.length > visibleCount"
    >
      <el-button>
        更多<i class="el-icon-arrow-down el-icon--right"></i>
      </el-button>
      <template #dropdown>
        <el-dropdown-menu>
          <template
            v-for="item in cmBtnList.slice(visibleCount)"
            :key="item.code"
          >
            <el-dropdown-item @click.stop.prevent="handleClick(item.code)"
              ><i :class="item.icon"></i> {{ item.label }}</el-dropdown-item
            >
          </template>
        </el-dropdown-menu>
      </template>
    </el-dropdown>
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
      visibleCount: Infinity,
      more: [],
      visibleWidth: 9999,
    };
  },
  mounted() {
    var objResizeObserver = new ResizeObserver(() => {
      console.log("-----");
      this.syncVisibleCount();
    });
    // 观察文本域元素
    objResizeObserver.observe(this.$refs.buttons);
    setTimeout(() => {
      this.syncVisibleCount();
    }, 3000);
    // var observerOptions = {
    //   childList: true, // 观察目标子节点的变化，是否有添加或者删除
    //   attributes: true, // 观察属性变动
    //   subtree: true, // 观察后代节点，默认为 false
    // };

    // var observer = new MutationObserver(() => {
    //   console.log("-----");
    //
    // });
    // observer.observe(this.$refs.buttons, observerOptions);
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
    syncVisibleCount() {
      let containerWidth = this.$refs.buttons.clientWidth - 110;
      let count = 0;
      let totalWidth = 0;

      Array.from(this.$refs.buttons.firstElementChild.children).forEach(
        (element) => {
          if (element.nodeName === "BUTTON") {
            let marginLeft = Number(
              getComputedStyle(element).marginLeft.slice(0, -2)
            );
            let marginRight = Number(
              getComputedStyle(element).marginRight.slice(0, -2)
            );
            let width = Number(getComputedStyle(element).width.slice(0, -2));
            if (
              totalWidth + width + marginLeft + marginRight <
              containerWidth
            ) {
              totalWidth += width + marginLeft + marginRight;
              count++;
            }
            console.log(width);
          }
        }
      );
      this.visibleWidth = totalWidth + 11;
      this.visibleCount = count;
      console.log("->", this.visibleCount);
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
<style scoped>
.container {
  display: flex;
}
.visible-button {
  overflow: hidden;
  white-space: nowrap;
  flex-shrink: 0;
}
</style>
