# Vue 3 + TypeScript 颜色混合计算

## 父子通信 --- v-model(父组件传值方式) + emit(子组件改值方式)
   - 问题描述： App组件引入了colorPicker组件， 想法是子组件colorPicker绑定的颜色 是由父组件传递过去的
      * 使用props的缺点： 父传子是单项数据流， 子组件不能直接更改props的值！不同于vue2在data定义新数据赋值的写法，
         **① 直接使用[newVariable=props.color]会导致失去响应式！！！（因为props.color是一个string类型的值数据）**
         **② 通过toRef解构出的变量由于仍然与props相联系， 所以会间接修改props，而vue是不允许的，控制台会报警告并且操作直接无效**

   - 使用v-model+emit的方式实现父子双向通信！
      * 父组件传值的时候 根据element-plus组件，接受数据使用 :model-value
      * 子组件定义emit事件， 注意： ***事件名称必须是['update:变量名']***
   
