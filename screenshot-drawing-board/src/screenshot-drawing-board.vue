<template>
  <div class="screenshot-drawing-board_screenshot-container">
    <div class="screenshot-drawing-board_button" v-show="showOptionButton">
      <button @click="save" :style="buttonStyle">保存</button>
      <button @click="undo" :style="buttonStyle">上一步</button>
      <button @click="redo" :style="buttonStyle">下一步</button>
    </div>
    <div id="screenshot-drawing-board_screenshot">
    </div>
  </div>
</template>

<script>
import html2canvas from 'html2canvas'
import SimpleDrawingBoard from 'simple-drawing-board'
import hotkeys from 'hotkeys-js'

export default {
  props: {
    showOptionButton: {
      type: Boolean,
      default: true
    },
    ignoreDomClass: {
      type: Array,
      default: []
    },
    ignoreDomId: {
      type: Array,
      default: []
    },
    lineColor: {
      type: String,
      default: '#ff0000'
    },
    lineSize: {
      type: Number,
      default: 6
    },
    buttonStyle: {
      type: String,
      default: ''
    },
    logging: {
      type: Boolean,
      default: false
    }
  },

  data() {
    return {
      sdb: null
    }
  },

  created() {
    this.init()
    this.setHotkeys();
  },

  methods: {
    init() {
      this.ignoreDomClass.forEach(className => {
        let modals = document.getElementsByClassName(className)
        for (let i = 0; i < modals.length; i++) {
          modals[i].setAttribute('data-html2canvas-ignore', 'true')
        }
      })

      this.ignoreDomId.forEach(IdName => {
        let modals = document.getElementById(IdName)
        modals.setAttribute('data-html2canvas-ignore', 'true')
      })

      html2canvas(document.body, { logging: this.logging })
        .then(canvas => {
          let screenshot = document.getElementById('screenshot-drawing-board_screenshot')
          canvas.setAttribute('style', 'width:100%')
          screenshot.appendChild(canvas)
          this.sdb = new SimpleDrawingBoard(screenshot.getElementsByTagName('canvas')[0], {
            lineColor: this.lineColor,
            lineSize: this.lineSize
          })

          let self = this
          this.sdb.ev.on('save', function(curImg) {
            self.$emit('change', curImg)
          })
        })
        .catch(e => {
          console.error(e)
        })
    },

    setHotkeys(){
      let self = this;
      hotkeys('ctrl+z',function(event, handler){
        self.undo();
      });
      hotkeys('ctrl+y',function(event, handler){
        self.redo();
      });
    },

    clear() {
      let screenshot = document.getElementById('screenshot-drawing-board_screenshot')
      let canvas = screenshot.getElementsByTagName('canvas')[0]
      screenshot.removeChild(canvas)
    },

    getImg() {
      return this.sdb.getImg()
    },

    save() {
      if (!this.sdb) {
        return
      }
      let img = this.sdb.getImg()
      this.$emit('save', img)
    },

    undo() {
      this.sdb && this.sdb.undo()
    },

    redo() {
      this.sdb && this.sdb.redo()
    }
  }
}
</script>

<style scoped>
#screenshot-drawing-board_screenshot {
  border: 1px solid #dcdfe6;
  cursor: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABIAAAASCAYAAABWzo5XAAAABGdBT…5x2i2eWFcggTPIJd3Eiv5Zd3KlU3OJOQd9lVd4A5Xl1g4YG2GGhwRfegAAAABJRU5ErkJggg==);
  cursor: -webkit-image-seturl(
        data: image/png;base64,
        iVBORw0KGgoAAAANSUhEUgAAABIAAAASCAYAAABWzo5XAAAABGdBT…5x2i2eWFcggTPIJd3Eiv5Zd3KlU3OJOQd9lVd4A5Xl1g4YG2GGhwRfegAAAABJRU5ErkJggg==
      )
      0 17,
    default;
}

.screenshot-drawing-board_button button {
  display: inline-block;
  line-height: 1;
  white-space: nowrap;
  cursor: pointer;
  background: #fff;
  border: 1px solid #dcdfe6;
  color: #606266;
  -webkit-appearance: none;
  text-align: center;
  box-sizing: border-box;
  outline: 0;
  margin: 0;
  transition: 0.1s;
  padding: 10px 20px;
  font-size: 14px;
  border-radius: 4px;
}
</style>
