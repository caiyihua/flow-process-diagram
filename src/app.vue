<template>
    <transition name="goAmion">
        <div v-if="show"  class="go"  @dragover.stop="(ev) => dragover(ev, -1 )">
            <div :style="{marginTop: (height/2 + 10) + 'px'}">
                <div v-for="(item,n) in data" v-if="n < data.length/num" :key="n" :style="{borderRight: n%2 === 0 && n < (data.length/num-1) && '2px solid #4b72cc',borderLeft: n%2 !== 0 && n < (data.length/num-1) && '2px solid #4b72cc',float: 'left',width: num* width +'px', paddingBottom: isOne && n > (data.length/num-1) ? 0 :   (height + 10) + 'px'}">
                    <ul :style="{float: n%2 !== 0 && 'right',width: num* width +'px'}">
                        <li v-if="index >= n*num && index < (n+1)*num" v-for="(it,index) in data" :key="index" :style="{height: Array.isArray(it) ? it.length > 1 && (it.length - 1)*(height+5) + 'px' : (height/2) + 'px', width: Array.isArray(it) && it.length > 1 && width + 'px', float: n%2 !== 0 && 'right'}">
                            <template v-if="!Array.isArray(it) || it.length === 1">
                                <div
                                        :id="'item-' + index"
                                        :style="{width: width + 'px', paddingBottom: (height/2 + 5) + 'px'}"
                                        @drop.stop="(ev) => drop(ev, index, it, n)"
                                        @dragover.stop="(ev) => dragover(ev, index)"
                                >
                                    <div
                                            :style="{float: n%2 !== 0 && 'right',height: height/2 + 'px', opacity: index === 0 && 0}"
                                            class="go-line"
                                    >
                                        <div :style="{float: n%2 !== 0 && 'left', marginTop: (-12) + 'px'}"><i style="float: left;" :class=" n%2 === 0 ? ['el-icon-caret-right']: ['el-icon-caret-left']"></i></div>
                                    </div>
                                    <div
                                            :draggable="draggable"
                                            @dragstart="($event) => drag($event, it, index)"
                                            @dragend.stop="($event) => dragEnd($event)"
                                            :style="{float: n%2 !== 0 && 'right', height: height + 'px', marginTop: (height/-2) + 'px', width: (width - 50) + 'px'}"
                                            class="go-item"
                                    >
                                        <slot name="items" :value="it" :index="index"></slot>
                                    </div>
                                    <div
                                            :style="{float: n%2 !== 0 && 'right',opacity: index === (data.length-1) && 0,height: height/2 + 'px'}"
                                            class="go-line"
                                    ></div>
                                </div>
                            </template>
                            <template v-else>
                                <div class="sline" :style="{borderRight: '2px solid #4b72cc',opacity: n%2 !== 0 && index === (data.length-1) && 0}"></div>
                                <div v-for="(its, i) in it" :key="i" :style="{width: width + 'px',marginLeft: '4px',float: n%2 !== 0 ? 'right': 'left',marginRight: '4px', paddingBottom: (height/2 + 5) + 'px'}">
                                    <div :style="{float: n%2 !== 0 && 'right', width: '21px',height: height/2 + 'px'}"  class="go-line">
                                        <div :style="{float: n%2 !== 0 && 'left', marginTop: (-12) + 'px', height: '24px'}"><i style="float: left" :class=" n%2 === 0 ? ['el-icon-caret-right']: ['el-icon-caret-left']"></i></div>
                                    </div>
                                    <div :style="{float: n%2 !== 0 && 'right', height: height + 'px', marginTop: (height/-2) + 'px', width: (width - 50) + 'px'}" class="go-item">
                                        <slot name="items" :value="its" :index="index"></slot>
                                    </div>
                                    <div :style="{float: n%2 !== 0 && 'right',opacity: index === (data.length-1) && 0, width: '21px',height: height/2 + 'px'}"  class="go-line"></div>
                                </div>
                                <div class="sline"  :style="{borderLeft: '2px solid #4b72cc',opacity: n%2 === 0 && index === (data.length-1) && 0, right:  0  }"></div>
                            </template>
                        </li>
                    </ul>
                </div>
            </div>
        </div>
    </transition>
</template>

<script >
  export default {
    name: 'ewGo',
    props:{
      data:{
        type: Array //数据 如[{name:1}, [{name:2},{name:3}]]  <div class="item" slot="items" slot-scope="scope">{{scope.value.name}}</div> 样式自己设置class-item
      },
      num: {
        type: Number //一行显示几个
      },
      height: {
        type: Number, //一项 高度
        default: 30
      },
      width: {
        type: Number, //宽度
        default: 200
      },
      draggable: {
        type: Boolean, //能否拖动
        default: false
      },
      show: {
        type: Boolean, //能否显示
        default: true
      },
      isOne: {
        type: Boolean, //是否只有一条 没有并列关系
        default: false
      }
    },
    data() {
      return {
        dropItem:'',
        dropIndex: 0,
        dropEv:''
      }
    },
    beforeUpdate () {
      this.show = false
    },
    updated() {
      this.show = true
    },
    mounted() {
//        document.querySelector('.el-dialog__wrapper').addEventListener('drop', function(e) {
//          e.stopPropagation()
//          e.preventDefault()
//          console.log(11111111111111111111111111111111111)
//        },true)
    },
    methods: {
      drag(ev, val, index) {
        this.dropEv = ev
        this.dropItem = val
        this.dropIndex = index
        ev.dataTransfer.setData("text", ev.target.id);
        this.$emit('overStyle', ev.target.children[0])
      },
      dragover(ev, index) {
        ev.preventDefault()
//        if(index === -1) {
//          this.dropEv.target.style.background = '#fff'
//        }
//        else{
//          console.log(666)
//          this.dropEv.target.style.background = '#000'
//        }
      },
      drop(ev,index, item, num) {
        ev.preventDefault()
        if(index !== -1 ) {
          let addIndex = 0
          if(num%2 === 0 ) {
            addIndex = ev.layerX > this.width/2 ||  (ev.layerX + 30) > this.width/2? (index+1) : index
          }
          else {
            addIndex = ev.layerX > this.width/2 ||  (ev.layerX + 30) > this.width/2? index : (index+1)
          }
          if(this.dropItem !== item) {
            this.show = false
            let res = JSON.parse(JSON.stringify(this.dropItem))
            this.data[this.dropIndex].name = -1
            this.data.splice(addIndex, 0, res)
            let deleIndex = this.data.findIndex(it => (it.name === -1))
            this.data.splice(deleIndex, 1)
            setTimeout(() => {
              this.show = true
            },0)
          }
        }
      },
      dragEnd(ev) {
        ev.preventDefault()
        this.$emit('endStyle', this.dropEv.target.children[0])
      }
    }
  }
</script>

<style lang="less">
    .go{
        float: left;
        /*margin: auto;*/
        padding: 0 30px;
        div{
            box-sizing: border-box;
        }
        ul{
            float: left;
            li{
                float: left;
                position: relative;
                .sline{
                    width: 5px; height: 100%;
                    position: absolute;
                    border-top:  2px solid #4b72cc;
                    margin-top: 0px;
                    padding-top: 0px;

                }
                > div {
                    float: left;
                    margin-top: -40px;
                    padding-top: 40px;
                    .go-item{
                        float: left;
                        transition: transform 0.2s;
                    }
                    .go-line{
                        float: left;
                        width: 25px;
                        border-top: 2px solid #4b72cc;
                        div{
                            float: right;
                            margin-right: -7px;
                            margin-left: -5px;
                            color: #4b72cc;
                            font-size: 24px;
                        }
                    }
                    .go-item:hover{
                        transform: scale(1.1);
                    }
                }
            }
        }
    }
    .goAmion-enter-active {
        transition: opacity 1s;
    }
    .goAmion-leave-active {
        opacity: 0;
    }
    .goAmion-enter, .goAmion-leave-to /* .fade-leave-active below version 2.1.8 */ {
        opacity: 0;
    }
</style>