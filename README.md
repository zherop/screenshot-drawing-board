# screenshot-drawing-board

A vue component that contains screenshot and drawingboard.

这是一个Vue组件，支持截屏，简易画板功能，最终生成图片。

### Install

```sh
npm install screenshot-drawing-board
```

### How to use

- template

```html
<screenshot-drawing-board ref="sdb"
    :showOptionButton="false"
    @change="onChange"
    :ignoreDomClass="ignoreDomClass"
    :ignoreDomId="ignoreDomId">
</screenshot-drawing-board>
```
- script

```js
<script>
import screenshotDrawingBoard from 'screenshot-drawing-board'
export default {
  components:{
    screenshotDrawingBoard
  },

  data(){
    return {
      screenshot:null,
      ignoreDomClass:['v-modal','el-dialog__wrapper'],
      ignoreDomId:[]
    }
  },

  methods:{
    onChange(img){
      // 'data:image/png;base64,xxxxxx....'
      this.screenshot = img;
    }
  }
}
</script>
```

### Attributes

| 参数             |     说明 	                                |	类型  |	可选值      | 默认值  |
| ---------------- | ------------------------------------------ |-------- | ----------- | ------- |
| showOptionButton | 是否显示操作按钮                           | Boolean | true, false | true    |
| ignoreDomClass   | 忽略的class，截屏时不包含该class对应的dom  | Array   | -           | []      |
| ignoreDomId      | 忽略的id，截屏时不包含该id对应的dom        | Array   | -           | []      |
| lineColor 	   | 画笔的颜色                                 | String  | -           | #ff0000 |
| lineSize         | 画笔的尺寸                                 | Number  | -           | 6       |
| buttonStyle      | 操作按钮的样式                             | String  | -           | -       |
| logging          | 是否打印控制台日志                         | Boolean | true, false | false   |



### Events

| 事件     |     说明 	                                |	参数       |	
| -------- | ------------------------------------------ |------------- | 
| change   | 当画板发生变化时会触发该事件               | img(base64)  |
| save     | 当点击保存按钮时会触发该事件               | img(base64)  |

### Methods

| 方法名   |     说明 	                                |	参数       |	
| -------- | ------------------------------------------ |------------- | 
| undo     | 当点击[上一步]按钮时会触发该事件           | -            |
| redo     | 当点击[下一步]按钮时会触发该事件           | -            |
| save     | 当点击[保存]按钮时会触发该事件             | -            |
| init     | 初始化画板                                 | -            |
| clear    | 销毁画板                                   | -            |

### Hotkeys

- ctrl + z

  撤销，回到上一步画笔步骤。

- ctrl + y

  回到下一步画笔步骤。

### Demo

  [GitHub](https://github.com/zherop/vue-example#screenshot-drawing-board)