<template>
  <div id="color-picker">
    <div class="mixer" :style="{background: color}">
      <el-color-picker
        @change="changeColorEmit"
        @active-change="activeChangeColorEmit"
        :model-value="color"
      />
      <!-- 
        element-plus的colorPicker组件是支持 v-model父子组件双向传递参数的
              :model-value --》 v-model子组件默认接受props参数名称就是 modelValue
       -->
      <!-- 

        注意：
          prop传递过来的值都是单向下行绑定，子组件不能修改由父组件传递过来的值！！！！
            所以不能直接使用v-model双向绑定

            直接使用 props的值 绑定给v-model会报错:
                v-model cannot be used on a prop, because local prop bindings are not writable.
                Use a v-bind binding combined with a v-on listener that emits update:x event instead.

        vue3中 如果要使用 v-model实现 父子通信， 需要结合 props和emit 实现

       -->
      <!-- 
        element-plus
          change 当前绑定的值发生变化时触发【v-model绑定的值】

          active-change 面板中当前显示的颜色发生改变时触发
            触发后会有一个默认颜色参数(即当前面板显示的颜色)
       -->
    </div>
    <div class="showRGB">
      <span>R <input type="text" maxlength="3" :value="RValue" @change="changeRValue" /></span>
      <span>G <input type="text" maxlength="3" :value="GValue" @change="changeGValue" /></span>
      <span>B <input type="text" maxlength="3" :value="BValue" @change="changeBValue" /></span>
    </div>
    <div class="showHEX">
      <!-- 
        element-plus的color-picker组件 这里默认是hex
            color-format	写入 v-model 的颜色的格式:	hex (当 show-alpha 为 false) / rgb (当 show-alpha 为 true)
       -->
      <span>H <input type="text" maxlength="2" :value="color.slice(1,3)" @change="changeHValue" /></span>
      <span>E <input type="text" maxlength="2" :value="color.slice(3,5)" @change="changeEValue" /></span>
      <span>X <input type="text" maxlength="2" :value="color.slice(5,7)" @change="changeXValue" /></span>
    </div>
  </div>
</template>

<script setup lang='ts'>
import { computed } from 'vue';

// 十进制转十六进制
import deciToHexa from '../utils/decToHex'


// 接收父组件传递过来的颜色值
const props = defineProps<{
  color: string
}>()
console.log(props.color)
// v-model传参：  父子组件参数双向绑定


const emit = defineEmits<{
  // 对象中可以传递多个函数
  // 函数第一个参数是【函数的名称】， 后面的参数是【要传递的参数】！
  (e: 'update:color', color: string): void,
  // (e: 'changeColor', color: string): void,
  // (e: 'activeChangeColor', color: string): void
}>()

// 改变颜色， 触发change函数
// 当绑定值变化时触发
const changeColorEmit = (value) => {
  // 调用定义好的emit函数
  emit('update:color', value)
}
const activeChangeColorEmit = (value) => {
  emit('update:color', value)
}

// 改变 H / E / X
const changeHValue = (e: Event) => {
  console.log(e.target)
  const target = e.target as HTMLInputElement

  // 校验
  console.log(/^[0-9A-Fa-f]{2}$/g.test(target.value))
  if (!/^[0-9A-Fa-f]{2}$/g.test(target.value)) {
    target.value = props.color.slice(1,3)
    alert('请输入合法内容！')
  }
  if ( parseInt(target.value, 16) > 255) {
    target.value = 'FF'
  }

  // console.log(target.value )
  // console.log(props.color.match(/[A-Fa-f0-9]{1,2}/) )
  emit('update:color', props.color.replace(/[A-Fa-f0-9]{1,2}/, target.value))
}
const changeEValue = (e: Event) => {
  console.log(e.target)
  const target = e.target as HTMLInputElement
  // console.log(target.value )
  /* 
    (?<=ABC) 
      Matches a group before the main expression without including it in the result.
      匹配一个表达式，这个表达式前面有组合ABC， 但是最终的结果 不包含 ABC 这个组合
  */
  console.log(props.color.match(/(?<=[A-Fa-f0-9]{2})[A-Fa-f0-9]{1,2}/) )
  emit('update:color', props.color.replace(/(?<=[A-Fa-f0-9]{2})[A-Fa-f0-9]{1,2}/, target.value))
}
const changeXValue = (e: Event) => {
  console.log(e.target)
  const target = e.target as HTMLInputElement
  emit('update:color', props.color.replace(/(?<=[A-Fa-f0-9]{4})[A-Fa-f0-9]{1,2}/, target.value))
}



// R / G / B
/* 
  HEX: #ff0000
  r: f*16 + f = 15*16+15
  g: 0*16 + 0
  b: 0*16 + 0

  HEX: #1722DF
  r: 1*16 + 7 = 15*16+15
  g: 2*16 + 2 = 34
  b: D*16 + F = 13*16+15
*/
const RValue = computed({
  get: () => {
    return parseInt(props.color.slice(1,3), 16)
  },
  set: (newValue) => {
    console.log(newValue)
  }
})
const GValue = computed(() => {
  return parseInt(props.color.slice(3,5), 16)
})
const BValue = computed(() => {
  return parseInt(props.color.slice(5,7), 16)
})

const changeRValue = (e: Event) => {
  
  const target = e.target as HTMLInputElement

  // 校验
  // console.log(/[0-9]/g.test(target.value))
  if (!/[0-9]/g.test(target.value)) {
    target.value = (RValue.value).toString()
    alert('请输入数字！')
  }
  if ( parseInt(target.value) > 255) {
    target.value = '255'
  }

  const vLeft: string = parseInt(target.value) == 0 ? '0' : deciToHexa(Math.floor(parseInt(target.value) / 16))
  const vRight: string = parseInt(target.value) == 0 ? '0' : deciToHexa(parseInt(target.value) % 16)
  console.log(vLeft+vRight);
  RValue.value = parseInt(target.value)
  
  emit('update:color', props.color.replace(/[A-Fa-f0-9]{1,2}/, vLeft+vRight))
}
const changeGValue = (e: Event) => {
  const target = e.target as HTMLInputElement

  // 校验
  // console.log(/[0-9]/g.test(target.value))
  if (!/[0-9]/g.test(target.value)) {
    target.value = (RValue.value).toString()
    alert('请输入数字！')
  }
  if ( parseInt(target.value) > 255) {
    target.value = '255'
  }

  const vLeft: string = parseInt(target.value) == 0 ? '0' : deciToHexa(Math.floor(parseInt(target.value) / 16))
  const vRight: string = parseInt(target.value) == 0 ? '0' : deciToHexa(parseInt(target.value) % 16)
  console.log(vLeft+vRight);
  RValue.value = parseInt(target.value)
  
  emit('update:color', props.color.replace(/(?<=[A-Fa-f0-9]{2})[A-Fa-f0-9]{1,2}/, vLeft+vRight))
}
const changeBValue = (e: Event) => {
  const target = e.target as HTMLInputElement

  // 校验
  // console.log(/[0-9]/g.test(target.value))
  if (!/[0-9]/g.test(target.value)) {
    target.value = (RValue.value).toString()
    alert('请输入数字！')
  }
  if ( parseInt(target.value) > 255) {
    target.value = '255'
  }

  const vLeft: string = parseInt(target.value) == 0 ? '0' : deciToHexa(Math.floor(parseInt(target.value) / 16))
  const vRight: string = parseInt(target.value) == 0 ? '0' : deciToHexa(parseInt(target.value) % 16)
  console.log(vLeft+vRight);
  RValue.value = parseInt(target.value)
  
  emit('update:color', props.color.replace(/(?<=[A-Fa-f0-9]{4})[A-Fa-f0-9]{1,2}/, vLeft+vRight))
}

// decimalToHexadecimal 十进制数字转为十六进制数字
// App.vue也用到了， 所有放 公共方法 utils 里面了
// const deciToHexa = (num: number) => {
//   switch (num ) {
//     case 10:
//       return 'A';
//     case 11:
//       return 'B';
//     case 12:
//       return 'C'
//     case 13:
//       return 'D';
//     case 14:
//       return 'E';
//     case 15:
//       return 'F';
//     default:
//       return num.toString();
//   }
//   // switch (key) {
//   //   case value:
      
//   //     break;
  
//   //   default:
//   //     break;
//   // }
// }



</script>

<style scoped>
/* element-plus 修改默认样式 */
:deep(.el-color-picker__trigger) {
    height: 150px !important;
    width: 300px;
    font-size: 0;
    cursor: pointer;
    border: none;
    padding: 0;
    opacity: 0;
}
.mixer {
  width: 300px;
  height: 150px;
  /* border: 1px solid #ccc; */
  padding: 10px;
  box-shadow: 2px 2px 10px rgb(119, 119, 119, 0.2);
}

.showRGB, .showHEX {
  width: 300px;
  height: 50px;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 0 10px;
}
.showRGB input, .showHEX input {
  width: 25px;
  height: 25px;
  margin-right: 20px;
  border-color: #ccc;
  outline: none;
  /* padding: 5px; */
  text-align: center;
}
</style>
