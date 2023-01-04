<!-- 
  parseInt(string, radix) 解析一个字符串并【返回】指定基数的【十进制整数】

  replace()
    参数一： 字符串 或者 RegExp正则表达式
        为什么不能使用 slice()?
        傻瓜！！！！
        可以使用 slice()的, 没生效是 因为 你写的表达式 并没有 指定 要替换的字符串的 具体位置啊啊啊啊啊啊！！！蠢货！！！！
        例如 replace(color3.value.slice(5),'ff'), 最终结果: 第一个参数 仅仅是一个字符串，例如'00'
        并且replace只会执行一次， 例如原字符串为 #00ff00, 此时匹配到第一个00后 就直接替换为 ff 啦！！！！白痴！！！！
    参数二： 用来替换的字符串


-->

<script setup lang="ts">
import { computed, ref } from 'vue';
import ColorPicker from './components/ColorPicker.vue'


// 十进制转十六进制
import deciToHexa from './utils/decToHex'

// 传递给color-picker组件中 v-model绑定的颜色值
const color1 = ref('#ff0000')
const color2 = ref('#ff0000')
const color3 = ref('#ff0000')

/* 子组件1 */
// v-model绑定的值发生改变时
const changeColor1 = (color) => {
  // console.log(color)
  color1.value = color
}
// 颜色面板发生变化时
const activeChangeColor1 = (activeColor) => {
  // console.log(activeColor)
  color1.value = activeColor
}


/* 子组件2 */
// v-model绑定的值发生改变时
const changeColor2 = (color) => {
  // console.log(color)
  color2.value = color
}
// 颜色面板发生变化时
const activeChangeColor2 = (activeColor) => {
  // console.log(activeColor)
  color2.value = activeColor
}

/* 子组件3 */
// v-model绑定的值发生改变时
const changeColor3 = (color) => {
  // console.log(color)
  color3.value = color
}
// 颜色面板发生变化时
const activeChangeColor3 = (activeColor) => {
  // console.log(activeColor)
  color3.value = activeColor
}

// 混合颜色
const mixColor = () => {
  console.log('color1', color1.value);
  console.log('color2', color2.value);
  
  let sum1 = parseInt(color1.value.slice(1,3), 16) + parseInt(color2.value.slice(1,3), 16)
  console.log('sum1', sum1)
  if ( sum1 > 255) {
    color3.value = color3.value.replace(/[A-Fa-f0-9]{1,2}/, 'FF')
    console.log('超255啦', color3.value);
  } else {
    const vLeft: string = sum1 == 0 ? '0' : deciToHexa(Math.floor(sum1 / 16))
    const vRight: string = sum1 == 0 ? '0' : deciToHexa(sum1 % 16)
    color3.value = color3.value.replace(/[A-Fa-f0-9]{1,2}/, vLeft+vRight)
    console.log('color3', color3.value);
  }


  let sum2 = parseInt(color1.value.slice(3,5), 16) + parseInt(color2.value.slice(3,5), 16)
  if ( sum2 > 255) {
    color3.value = color3.value.replace(/(?<=[A-Fa-f0-9]{2})[A-Fa-f0-9]{1,2}/, 'FF')
    console.log('sum2超255啦', color3.value);
  } else {
    const vLeft: string = sum2 == 0 ? '0' : deciToHexa(Math.floor(sum2 / 16))
    const vRight: string = sum2 == 0 ? '0' : deciToHexa(sum2 % 16)
    color3.value = color3.value.replace(/(?<=[A-Fa-f0-9]{2})[A-Fa-f0-9]{1,2}/, vLeft+vRight)
  }


  let sum3 = parseInt(color1.value.slice(5), 16) + parseInt(color2.value.slice(5), 16)
  if ( sum3 > 255) {
    // 错误写法！！！！白痴！白痴！白痴！！！
    // color3.value = color3.value.replace(color3.value.slice(5), 'FF')
    // const b = color3.value.slice(5)
    // console.log(b, '-----', typeof b);
    // color3.value = color3.value.replace(b, 'FF')
    /* 
      白痴吧你！！！！！！
        没生效是 因为 你写的表达式 并没有 指定 要替换的字符串的 具体位置啊啊啊啊啊啊！！！蠢货！！！！

        这里 replace(color3.value.slice(5),'ff'), 最终结果: 第一个参数 仅仅是一个字符串，例如'00'

        并且replace只会执行一次， 这里原字符串为 #00ff00, 所以匹配到第一个00后 就直接替换为 ff 啦！！！！白痴！！！！
    */
    
    color3.value = color3.value.replace(/(?<=[A-Fa-f0-9]{4})[A-Fa-f0-9]{1,2}/, 'FF')
    console.log('sum3超255啦', color3.value);
  } else {
    const vLeft: string = sum3 == 0 ? '0' : deciToHexa(Math.floor(sum3 / 16))
    const vRight: string = sum3 == 0 ? '0' : deciToHexa(sum3 % 16)
    color3.value = color3.value.replace(/(?<=[A-Fa-f0-9]{4})[A-Fa-f0-9]{1,2}/, vLeft+vRight)
  }
  
  console.log('color3', color3.value);
}
</script>

<template>
  <header></header>
  <main class="wrapper">
    <!-- v-model实现父子双向数据绑定！见: README.md  '父子通信...' -->
    <color-picker
      v-model:color="color1"
      @change-color="changeColor1"
      @active-change-color="activeChangeColor1"
    >
    </color-picker>
    <p>+</p>
    <color-picker
      v-model:color="color2"
      @change-color="changeColor2"
      @active-change-color="activeChangeColor2"
    ></color-picker>
    <div class="btn">
      <button class="mixing" @click="mixColor">混合</button>
      <!-- <button class="difference">差值</button> -->
    </div>
    <color-picker
      v-model:color="color3"
      @change-color="changeColor3"
      @active-change-color="activeChangeColor3"
    ></color-picker>
  </main>
  <!-- <footer></footer> -->
</template>

<style scoped>
.wrapper {
  width: 1200px;
  height: 300px;
  display: flex;
  justify-content: space-between;
  /* align-items: center; */
  margin: 0 auto;
}
.wrapper p {
  line-height: 150px;
  font-size: 40px;
  color: #777;
}
.wrapper .btn {
  width: 50px;
  height: 150px;
  /* line-height: 150px; */
  display: flex;
  flex-direction: column;
  justify-content: center;
}
.wrapper .btn .mixing {
  margin-bottom: 10px;
}
.mixing, .difference {
  width: 50px;
  height: 30px;
}
</style>
