<template lang="html">
  <div class="picker" v-show="isShow">
    <div class="box">
      <div class="header">
        <div class="header-button">
          取消
        </div>
        <div class="header-button" @click="affirm">
          确定
        </div>
      </div>
      <div class="content-control" id="item_control">
        <div class="content-box" :id="index"  v-for="(item,index) in items">
        </div>
      </div>
      <div class="content" id="item_list">
        <div class="content-box" :id="'item'+index"  v-for="(item,index) in items">
            <div class="content-layer">
              <div class="item picker-color"  v-for="obj in item">
                  {{ obj.title }}
              </div>
            </div>
        </div>
      </div>
      <div class="content-clarity" id="item_list01">
        <div class="content-box"  :id="'item-'+index"  v-for="(item,index) in items">
            <div class="content-layer">
              <div class="item picker-color"  v-for="obj in item">
                  {{ obj.title }}
              </div>
            </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    list: Array,
    name: String,
    result: String
  },
  data () {
    return {
      y: 0,
      startY: 0,
      aboveY: 0,
      divHeight: 0,
      isShow: true,
      items: {}
    }
  },
  created () {
    this.items = JSON.parse(JSON.stringify(this.list).replace(new RegExp(this.name, 'g'), 'title'))
  },
  mounted () {
    this.$nextTick(function () {
      var list = document.getElementById('item_control').childNodes
      for (var i = 0; i < list.length; i++) {
        list[i].addEventListener('touchstart', this.start, true)
        list[i].addEventListener('touchmove', this.move, true)
        list[i].addEventListener('touchend', this.end, true)
      }
    })
  },
  methods: {
    start (e) {
      e.preventDefault()
      var node01 = document.getElementById('item' + e.target.getAttribute('id'))
      this.startY = e.changedTouches[0].pageY
      //  判断是否滑动过
      if (isNaN(parseInt(node01.style.top))) {
        this.aboveY = 0
      } else {
        this.aboveY = parseInt(node01.style.top)
      }
      //  单个元素高度
      this.divHeight = document.getElementById('item_list').childNodes[0].childNodes[0].childNodes[0].offsetHeight
    },
    move (e) {
      e.preventDefault()
      var node01 = document.getElementById('item' + e.target.getAttribute('id'))
      var node02 = document.getElementById('item-' + e.target.getAttribute('id'))
      //  判断是否是滑动层
      if (e.target.parentNode.className === 'content-control') {
        this.Y = e.changedTouches[0].pageY - this.startY
        var count = (this.aboveY + this.Y) + (this.divHeight * node01.childNodes[0].childNodes.length)
        //  判断是否能滑动
        if (this.aboveY + this.Y < 18 && count > -18) {
          node01.style.top = this.aboveY + this.Y + 'px'
          node01.style.transform = 'translate3d(0px, ' + (this.aboveY + this.Y) + 'px, 0px)'
          node02.style.transform = 'translate3d(0px, ' + (this.aboveY + this.Y) + 'px, 0px)'
        }
      }
    },
    end (e) {
      e.preventDefault()
      var node01 = document.getElementById('item' + e.target.getAttribute('id'))
      var count = parseInt(Math.round(parseInt(node01.style.top) / this.divHeight))
      var list = node01.childNodes[0].childNodes
      for (var i = 0; i < list.length; i++) {
        if (list[i].className === 'picker-select item picker-color') {
          list[i].className = 'item picker-color'
          break
        }
      }
      if (count > 0 || count < 0) {
        node01.childNodes[0].childNodes[-count - 1].setAttribute('class', 'picker-select item picker-color')
      }
      node01.style.top = count * this.divHeight + 'px'
      node01.style.transform = 'translate3d(0px, ' + (count * this.divHeight) + 'px, 0px)'
      document.getElementById('item-' + e.target.getAttribute('id')).style.transform = 'translate3d(0px, ' + (count * this.divHeight) + 'px, 0px)'
      this.affirm()
    },
    affirm () {
      var ret = []
      var box = document.getElementById('item_list').childNodes
      for (var i = 0; i < box.length; i++) {
        var list = box[i].childNodes[0].childNodes
        for (var y = 0; y < list.length; y++) {
          if (list[y].className === 'picker-select item picker-color') {
            ret.push(list[y].innerHTML.replace(/^(\s|\u00A0)+/, '').replace(/(\s|\u00A0)+$/, ''))
            break
          }
        }
      }
      this.$emit(this.result, ret)
    }
  },
  watch: {
    list () {
      this.items = JSON.parse(JSON.stringify(this.list).replace(new RegExp(this.name, 'g'), 'title'))
    }
  }
}
</script>
<style lang="stylus" rel="stylesheet/stylus">
  .picker
    position: absolute
    width: 100%
    height: 100%
    top: 0
    background-color: rgba(17, 15, 16, 0.5)
    .box
      position: absolute
      width: 100%
      bottom: 0
      background-color: #fff
      .content-control
        position: absolute
        display: flex
        height: 11.5rem
        overflow: hidden
        z-index: 20
        bottom: 0
        width:100%
        .content-box
          text-align:center
          flex: 1
      .content-clarity
        position: absolute
        width:100%
        z-index: 1
        line-height: 1.84rem
        top:5.52rem
        height: 1.84rem
        overflow: hidden
        font-size: 0.71rem
        display: flex
        background-color: #fff
        border-top:1px solid #999999
        border-bottom:1px solid #999999
        .content-box
          text-align:center
          flex: 1
          .content-layer
            margin-top: 1.84rem
            .picker-color
              color: #222222
            .item
              line-height: 1.84rem
              font-size: 0.71rem
      .header
        position: relative
        display: flex
        line-height: 1.84rem
        &:after
          content:' '
          position: absolute
          bottom:0
          width: 100%
          height: 1px
          background-color:#999999
          left: 0
        .header-button
          flex: 1
          font-size: 0.71rem
          padding: 0 0.56rem
        .header-button:nth-child(2)
          text-align: right
      .content
        position: relative
        display: flex
        height: 11.5rem
        overflow: hidden
        z-index: 1
        .content-box
          text-align:center
          flex: 1
          .content-layer
            margin-top: 5.52rem
            .picker-color
              color: #999999
            .item
              line-height: 1.84rem
              font-size: 0.71rem
</style>
