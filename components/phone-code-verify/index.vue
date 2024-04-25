<template>
  <div class="phone-code-verify-container">
    <div class="phone-code-verify-container-labels">
      <div v-for="(v, index) in codeArr" :key="index" class="phone-code-verify-container-labels-items"
        :style="v.cIsSelect ? 'border: 2px solid #707AFD;pointer-events:auto;' : 'border: 2px solid #E5E6EB;pointer-events:none;'">
        <input type="number" :id="v.cId" maxlength="1" v-model="v.cValue" @keyup="setValue(index)"
          @input="$forceUpdate()" @keyup.delete="onDelete(index)"/>
      </div>
    </div>
  </div>
</template>
<script setup>
import { ref, onMounted } from 'vue';

onMounted(()=>{
  setTimeout(() => {
    elementByIdSetFocus(0);
}, 100);
})
const props = defineProps({
  length: {
    type: Number,
    default: 4,
  },
  enableWord: {
    type: Boolean,
    default: false,
  },
})

var codeArr = ref([])

for (var i = 0; i < props.length; i++){
  let v = {
      cId: "code-id-" + i,//输入框控件id
      cValue: "",//输入框value
      cIsSelect: i == 0,//默认选中第一个
    }
  codeArr.value.push(v);
}


const isNumber = "^[0-9]*$"
const isNumberOrword = "^[A-Za-z0-9]+$"
const emit = defineEmits(["completed"])

function setValue(index) {
  // 验证输入
  var reg = null;
  if (props.enableWord) {
    reg = new RegExp(isNumberOrword);
  } else {
    reg = new RegExp(isNumber);
  }
  // 验证输入
  if (reg.test(codeArr.value[index].cValue)) {
    // 输入一个字母或者数字后,光标移动到下一个输入框
    nextFocus();
  } else {
    codeArr.value[index].cValue = "";
  }
}

function nextFocus() {
  let nextOne = codeArr.value.findIndex((value) => value.cValue == "");
  if (nextOne !== -1) {
    elementByIdSetFocus(nextOne);
  } else {
    var value = "";
    codeArr.value.forEach(v =>{
      value += v.cValue
    })
    emit("completed", value);
  }
}

function onDelete(index) {
  // 如果是第1格，不触发光标移动至上一个输入框
  let i = index == 0 ? 0 : index - 1;
  codeArr.value[i].cValue = "";
  elementByIdSetFocus(i);
}

function elementByIdSetFocus(index) {
  document.getElementById(codeArr.value[index].cId).focus();
  codeArr.value.forEach(v =>v.cIsSelect = false);
  codeArr.value[index].cIsSelect = true;
}
</script>
<style lang="scss">
.phone-code-verify-container-labels {
  display: flex;
  justify-content: space-between;
  align-items: center;

  .phone-code-verify-container-labels-items {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 77rpx;
    height: 96rpx;
    text-align: center;
    border: solid 2rpx #E5E6EB;
    border-radius: 4rpx;
    overflow: hidden;

    .phone-code-verify-container-labels-items input {
      border: none;
      outline: none;
      width: 77rpx;
      height: 96rpx;
      line-height: 96rpx;
      text-align: center;
      font-size: 25px;
    }
  }

}

</style>