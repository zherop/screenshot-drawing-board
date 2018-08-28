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
      default:  () => []
    },
    ignoreDomId: {
      type: Array,
      default:  () => []
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
    this.$nextTick(()=>{
      this.init()
      this.setHotkeys();
    });
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

          this.$emit('change',this.sdb.getImg())
          let self = this;
          this.sdb.ev.on('draw', function(coords) {
              self.$emit('change',self.sdb.getImg())
          });
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
  cursor: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABIAAAASCAYAAABWzo5XAAAABGdBTUEAALGPC/xhBQAAARpJREFUOBGdkr1KA0EUhTcxEkSQQIpAihQWPoedD2BnIVsZQiBVEkiR7SwtBDsJqXwBLQwEgoWNjY2FjYUQ38Ei5Oc7sBeGLTKze+Djzsyecxh2N4ryq0zkHpYQQ2H1SW5TNsxukaZzQsewACvTvIBg3eFUaABHMEv3OruBIN3icm8wZl+FZ3iBQ/AqweGW2FrlKlChV0McFsxOvaegm/T2lLzzTC/dqzaO7A1s/8GzE28Dhhj0f1jQnZ+c18CrKxxrcMO2/uK87m3AcAkrsKA7vzlvQJAmuNywrX84bwY1YBrBATyCFWj+QguCJKNCU1DZQ7r/Y55CsK5x2i2eWFcggTPIJd3Eiv5Zd3KlU3OJOQd9lVd4A5Xl1g4YG2GGhwRfegAAAABJRU5ErkJggg==) 0 17,default;
  cursor: -webkit-image-set(url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABIAAAASCAYAAABWzo5XAAAABGdBTUEAALGPC/xhBQAAARpJREFUOBGdkr1KA0EUhTcxEkSQQIpAihQWPoedD2BnIVsZQiBVEkiR7SwtBDsJqXwBLQwEgoWNjY2FjYUQ38Ei5Oc7sBeGLTKze+Djzsyecxh2N4ryq0zkHpYQQ2H1SW5TNsxukaZzQsewACvTvIBg3eFUaABHMEv3OruBIN3icm8wZl+FZ3iBQ/AqweGW2FrlKlChV0McFsxOvaegm/T2lLzzTC/dqzaO7A1s/8GzE28Dhhj0f1jQnZ+c18CrKxxrcMO2/uK87m3AcAkrsKA7vzlvQJAmuNywrX84bwY1YBrBATyCFWj+QguCJKNCU1DZQ7r/Y55CsK5x2i2eWFcggTPIJd3Eiv5Zd3KlU3OJOQd9lVd4A5Xl1g4YG2GGhwRfegAAAABJRU5ErkJggg==) 1x,url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACQAAAAkCAYAAADhAJiYAAAABGdBTUEAALGPC/xhBQAAAn9JREFUWAnFlz1IV2EUxs2+ixCCrCCrIaiGGnLIRdBacqkhtCEbqqVaoqmacpIma6rFHHITh1pKMKFaInATkb4hKChKIwj7rt8D74XDy3tfKM69Hng4zz3nvc9zfH3/93//DQ31xWqsBsEnMAU6wILFSpwnwB+Db/BDoPZYjuMYsMMU/Af1nron2o7hh5KBNNhP0AtqjV24vQPFzsT5F70jVU7UiPhNcNyY7IC/AfEwxfUsvcVmvRtdhNIQkNFvcAoUsQ3yGhRDxLmpWOiZryUMzxqDrfCXiTXjZo0bvZIwKnbhvHHZBH9q1s7Am03fhV42BsUQcb5knDbAp8EzsNHUXWgfKrF52XW/cVwH1265xgXUyszL6gOuExixc/8xjIZ8C9YbHRd6GpWyHcjV33PfTpcJjMgJuJ4xOeNU7yP37DY6LvQoKnrUpwxzNb12tLpMYES64foyzBmnep+5p83ouNCDqHwHKcNc7Qv3tLtMYEQOwPVClTNO9ea5Z7/RcaH7UJFwyjBX0x/Q5TKBEdFWa8tzxqme3gTdX0/3IqrDmDLM1XTodfhdYw9qcyBnnOrpcdDrOglienDpAZYyzNX0oDwJ3GMUxZxxWe+M+yRBcBX53j8OpS9Z9ziM4tKguoJ8B5Tthq1fDPe4p+co3gbLgrLyLWDNY94X1rqnFmN8F64dUiwBIyAeRNd6Za0sjqFsTXWGdJYU+q00DGz/qhpVxg3EraH4Q7AmmDaSB4Hq10Ot0vQimMVDPaLeFJz1A1DvQsqVxmbU40Hs9ST9tZVOYMT1r+gw1zF9ReEx2BI3qrrWp6jTiH+FPwD6pI2BJ6DW0HkYBzNAQ9wH82DB4i/kUnkzGX+skQAAAABJRU5ErkJggg==) 2x) 0 17,default
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
