<template>
  <div class="contentBox">
    <div id="btn1">1</div>
    <div id="btn2">2</div>
    <div id="btn3">3</div>
    <div id="btn4">4</div>
  </div>
  <div v-if="show" ref="guideModalRef" class="guide-modal">
    <div ref="guideBoxRef" class="guide-box">
      <div>{{ message }}</div>
      <button class="btn" :disabled="index === 0" @click="changeStep(true)">
        上一步
      </button>
      <button class="btn" @click="changeStep(false)">下一步</button>
    </div>
  </div>
</template>
<script lang="ts" setup>
import { selectors } from "@/utils/CONSTANT.js";
import { computed, onMounted, ref } from "vue";
const show = ref(true);
const index = ref(0);
const guideBoxRef = ref(null);
let preNode: any = null;

let message = computed(() => {
  return selectors[index.value].message;
});

onMounted(() => {
  guideEvent();
});

const changeStep = (val: boolean) => {
  val ? index.value-- : index.value++;
  guideEvent();
};
const guideEvent = () => {
  // 所有的节点都走完
  if (index.value > selectors.length - 1) {
    show.value = false;
    return;
  }
  if (preNode) {
    preNode.style = `z-index:0`;
  }
  preNode = document.querySelector(selectors[index.value].selector);
  const target = preNode;
  target.style = `position:relative;
                    z-index:1000
                `;
  const { x, y, width, height } = target.getBoundingClientRect();

  if (guideBoxRef.value) {
    const halfClientHeight = guideBoxRef.value.clientHeight / 2;    
    const halfClientWidth = guideBoxRef.value.clientWidth / 2
    // 引导块的位置
    guideBoxRef.value.style = `
            left:${x - halfClientWidth/2}px;
            top:${y + height + 20 }px
        `;
  }
};

// 页面内容发生变化时，重新计算位置
window.addEventListener("resize", () => guideEvent());
window.addEventListener("scroll", () => guideEvent());
</script>
<style>
.contentBox >div {
  width: 100px;
  height: 100px;
  background-color: darkgoldenrod;
  display: inline-flex;
  margin-right: 20px;
  z-index: 88;
  justify-content: center;
  align-items: center;
}
.guide-modal {
  position: fixed;
  z-index: 999;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  background-color: rgba(35, 34, 34, 0.3);
}
.guide-box {
  width: 150px;
  min-height: 10px;
  border-radius: 5px;
  background-color: #fff;
  position: absolute;
  transition: 0.5s;
  padding: 10px;
  text-align: center;
}
.btn{
    margin-top: 15px;
    margin-right: 5px;
}
</style>
