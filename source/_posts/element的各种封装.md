# element的各种封装
## Form
### Select 选择器 基础封装
#### MySelect 组件
```html
<template>
  <el-select
    v-model="value"
    v-bind="props"
    @input="onInput"
  >
    <el-option
      v-for="(item,index) in list"
      :key="item.value"
      :label="item.label"
      :value="item.value"
    />
  </el-select>
</template>

<script>
export default {
  name:'MySelect',
  props: {
    value: String,
    props: Object
  },
  data() {
    return {
      list:[{
        label:'123',
        value:1
      },
      {
        label:'456',
        value:1
      }]
    }
  },
  methods: {
    onInput(val) {
      this.$emit('input', val)
    }
  }
}
</script>

```
#### MySelect 调用
```html
<template>
  <MySelect v-model='value'>
</template>
<script>
export default {
  data(){
    return{
      value:''
    }
  }
}
</script>
```