<template>
  <view class="con">
    <movable-area class="area" :style="{ height: areaHeight }" @mouseenter="mouseenter" @mouseleave="mouseleave">
      <block v-for="(item, index) in imageList" :key="item.id">
        <movable-view
          class="view"
          :x="item.x"
          :y="item.y"
          direction="all"
          :damping="40"
          :disabled="item.disable"
          @change="onChange($event, item)"
          @touchstart="touchstart(item)"
          @mousedown="touchstart(item)"
          @touchend="touchend(item)"
          @mouseup="touchend(item)"
          :style="{ width: viewWidth + 'px', height: viewWidth + 'px', 'z-index': item.zIndex, opacity: item.opacity }"
        >
          <view class="area-con" :style="{ width: childWidth, height: childWidth, transform: 'scale(' + item.scale + ')' }">
            <image class="pre-image" :src="item.src" mode="aspectFill"></image>
            <view class="del-con" @click="delImage(item, index)" @touchstart.stop="delImageMp(item, index)" @touchend.stop="nothing()" @mousedown.stop="nothing()" @mouseup.stop="nothing()">
              <view class="del-wrap">
                <image
                  class="del-image"
                  src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACEAAAAhCAYAAABX5MJvAAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsMAAA7DAcdvqGQAAAAhdEVYdENyZWF0aW9uIFRpbWUAMjAyMDowNzoyNSAyMTo1NDoyOU4TkJAAAADcSURBVFhH7ZfRCoMwDEXLvkjwwVf/bH/emmAyN6glTW9WBjsgwm28OeCLpj81Sil7zvlJ90UiONS/yY5VogsO6XrBg3IEQ5a/s8vRSWUAKmLqp2w5jz5BiNQEGMo3GbloDLtFXJ1IkaEuhAiiY6gEIqB4yqACSk9piIBiKQ8VUFpLviKg3C2rESKgWERCBZSWiEfgIfffYvrrsAgoISJ3Apy3zuTxcSxLQkV6ykNEPKVQkZEyiAiiZKgDIaC4upACSlcn5fM/+WuDCAHF1E/Z/N9AhkMZnPNDPI+UDjPIXgAQIGjNAAAAAElFTkSuQmCC"
                ></image>
              </view>
            </view>
          </view>
        </movable-view>
      </block>
      <view
        class="add"
        v-if="imageList.length < number"
        :style="{ top: add.y, left: add.x, width: viewWidth + 'px', height: viewWidth + 'px' }"
        @click="addImages"
      >
        <view class="add-wrap" :style="{ width: childWidth, height: childWidth }">
          <image
            style="width: 54rpx;height: 54rpx;"
            src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADYAAAA2CAYAAACMRWrdAAABIUlEQVRoQ+2a2w2DMAxFeQzWrsMUbadAsEw3S1CqVgppKwLX8BEOP4iHTXx8uUgWdVXoVhdaV0VhSmf7vr/H8V3XzY6V3P9iD+nYOI5P7/01LMI596AwoZV0TIBXIUWFXhKLFBWYSFGhhxQN6SFFQ5i4ogITKSr0cEVDekjRECauqMBEigq9U7piOk2yAti27SUe5ljlTfPEQ6KZecTvwl4P3ytvOv06R2HDMNzes7+6aRrvnHvtf50L13Lp50rx88zcvNlS3JpwKQ67XyK04nq2nFbk/LqVjin0TvmBNgQ2S4UUDcliHgpMpKjQwxUN6SFFQ5i4ogITKSr0cEVDekjRECauqMAsVoph+hVPtYr5+03p9tbYQ96xrYtT4ootbAJGVxxVTapVswAAAABJRU5ErkJggg=="
          ></image>
        </view>
      </view>
    </movable-area>
  </view>
</template>

<script>
export default {
  data() {
    return {
      imageList: [],
      width: 0,
      add: {
        x: 0,
        y: 0
      },
      colsValue: 0,
      viewWidth: 0,
      tempItem: null,
      timer: null,
      changeStatus: true,
      preStatus: true,
    }
  },
  props: {
    // 返回排序后图片
    list: {
      type: Array,
      default: function() {
        return []
      }
    },
    // 选择图片数量限制
    number: {
      type: Number,
      default: 6
    },
    // 图片父容器宽度（实际显示的图片宽度为 imageWidth / 1.1 ），单位 rpx
    imageWidth: {
      type: Number,
      default: 230
    },
    // 图片列数（cols > 0 则 imageWidth 无效）
    cols: {
      type: Number,
      default: 0
    },
    // 图片周围空白填充，单位 rpx
    padding: {
      type: Number,
      default: 10
    },
    // 拖动图片时放大倍数 [0, ∞)
    scale: {
      type: Number,
      default: 1.1
    },
    // 拖动图片时不透明度
    opacity: {
      type: Number,
      default: 0.7
    },
    // 自定义添加（需配合 @aaddImage 事件使用）
    custom: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    areaHeight() {
      if (this.imageList.length < this.number) {
        return Math.ceil((this.imageList.length + 1) / this.colsValue) * this.viewWidth + 'px'
      } else {
        return Math.ceil(this.imageList.length / this.colsValue) * this.viewWidth + 'px'
      }
    },
    childWidth() {
      return this.viewWidth - this.rpx2px(this.padding) * 2 + 'px'
    },
  },
  created() {
    this.width = uni.getSystemInfoSync().windowWidth
    this.viewWidth = this.rpx2px(this.imageWidth)
  },
  mounted() {
    const query = uni.createSelectorQuery().in(this)
    query.select('.area').boundingClientRect(data => {
      this.colsValue = Math.floor(data.width / this.viewWidth)
      if(this.cols > 0){
        this.colsValue = this.cols
        this.viewWidth = data.width / this.cols
      }
      for (let item of this.list) {
        this.addProperties(item)
      }
    })
    query.exec()
  },
  methods: {
    onChange(e, item) {
      if(!item) return
      item.oldX = e.detail.x
      item.oldY = e.detail.y
      if (e.detail.source === 'touch') {
        if(item.moveEnd){
          item.offset = Math.sqrt(Math.pow(item.oldX - item.absX * this.viewWidth, 2) + Math.pow(item.oldY - item.absY * this.viewWidth, 2))
        }
        let x = Math.floor((e.detail.x + this.viewWidth / 2) / this.viewWidth)
        if(x >= this.colsValue) return
        let y = Math.floor((e.detail.y + this.viewWidth / 2) / this.viewWidth)
        let index = this.colsValue * y + x
        if (item.index != index && index < this.imageList.length) {
          this.changeStatus = false
          for (let obj of this.imageList) {
            if (item.index > index && obj.index >= index && obj.index < item.index) {
              this.change(obj, 1)
            } else if (item.index < index && obj.index <= index && obj.index > item.index) {
              this.change(obj, -1)
            } else if(obj.id != item.id) {
              obj.offset = 0
              obj.x = obj.oldX
              obj.y = obj.oldY
              setTimeout(() => {
                this.$nextTick(() => {
                  obj.x = obj.absX * this.viewWidth
                  obj.y = obj.absY * this.viewWidth
                })
              }, 0)
            }
          }
          item.index = index
          item.absX = x
          item.absY = y
          this.sortList()
        }
      }
    },
    change(obj, i){
      obj.index += i
      obj.offset = 0
      obj.x = obj.oldX
      obj.y = obj.oldY
      obj.absX = obj.index % this.colsValue
      obj.absY = Math.floor(obj.index / this.colsValue)
      setTimeout(() => {
        this.$nextTick(() => {
          obj.x = obj.absX * this.viewWidth
          obj.y = obj.absY * this.viewWidth
        })
      }, 0)
    },
    touchstart(item) {
      this.imageList.forEach(v => {
        v.zIndex = v.index + 9
      })
      item.zIndex = 99
      item.moveEnd = true
      this.tempItem = item
      this.timer = setTimeout(() => {
        item.scale = this.scale
        item.opacity = this.opacity
        clearTimeout(this.timer)
        this.timer = null
      }, 200)
    },
    touchend(item) {
      this.previewImage(item)
      item.scale = 1
      item.opacity = 1
      item.x = item.oldX
      item.y = item.oldY
      item.offset = 0
      item.moveEnd = false
      setTimeout(() => {
        this.$nextTick(() => {
          item.x = item.absX * this.viewWidth
          item.y = item.absY * this.viewWidth
          this.tempItem = null
          this.changeStatus = true
        })
      }, 0)
    },
    previewImage(item){
      if(this.timer && this.preStatus && this.changeStatus && item.offset < 28.28){
        clearTimeout(this.timer)
        this.timer = null
        let src = this.list.findIndex(v => v === item.src)
        uni.previewImage({
          urls: this.list,
          current: src,
          success: () => {
            this.preStatus = false
            setTimeout(() => {
              this.preStatus = true
            }, 600)
          }
        })
      } else if (this.timer){
        clearTimeout(this.timer)
        this.timer = null
      }
    },
    mouseenter(){
      //#ifdef H5
      this.imageList.forEach(v => {
        v.disable = false
      })
      //#endif
      
    },
    mouseleave(){
      //#ifdef H5
      if(this.tempItem){
        this.imageList.forEach(v => {
          v.disable = true
          v.zIndex = v.index + 9
          v.offset = 0
          v.moveEnd = false
          if(v.id == this.tempItem.id){
            if (this.timer){
              clearTimeout(this.timer)
              this.timer = null
            }
            v.scale = 1
            v.opacity = 1
            v.x = v.oldX
            v.y = v.oldY
            this.$nextTick(() => {
              v.x = v.absX * this.viewWidth
              v.y = v.absY * this.viewWidth
              this.tempItem = null
            })
          }
        })
        this.changeStatus = true
      }
      //#endif
    },
    addImages() {
      if(this.custom){
        this.$emit('addImage')
      } else {
        let checkNumber = this.number - this.imageList.length
        uni.chooseImage({
          count: checkNumber,
          sourceType: ['album', 'camera'],
          success: res => {
            let count = checkNumber <= res.tempFilePaths.length ? checkNumber : res.tempFilePaths.length
            for (let i = 0; i < count; i++) {
              this.addProperties(res.tempFilePaths[i])
            }
          }
        })
      }
    },
    addImage(image){
      this.addProperties(image)
    },
    delImage(item, index){
      this.imageList.splice(index, 1)
      for (let obj of this.imageList) {
        if (obj.index > item.index) {
          obj.index -= 1
          obj.x = obj.oldX
          obj.y = obj.oldY
          obj.absX = obj.index % this.colsValue
          obj.absY = Math.floor(obj.index / this.colsValue)
          this.$nextTick(() => {
            obj.x = obj.absX * this.viewWidth
            obj.y = obj.absY * this.viewWidth
          })
        }
      }
      this.add.x = (this.imageList.length % this.colsValue) * this.viewWidth + 'px'
      this.add.y = Math.floor(this.imageList.length / this.colsValue) * this.viewWidth + 'px'
      this.sortList()
    },
    delImageMp(item, index){
      //#ifdef MP
      this.delImage(item, index)
      //#endif
    },
    sortList() {
      let list = this.imageList.slice()
      list.sort((a, b) => {
        return a.index - b.index
      })
      for (let i = 0; i < list.length; i++) {
        list[i] = list[i].src
      }
      this.$emit('update:list', list)
    },
    addProperties(item){
      let absX = this.imageList.length % this.colsValue
      let absY = Math.floor(this.imageList.length / this.colsValue)
      let x = absX * this.viewWidth
      let y = absY * this.viewWidth
      this.imageList.push({
        src: item,
        x,
        y,
        oldX: x,
        oldY: y,
        absX,
        absY,
        scale: 1,
        zIndex: 9,
        opacity: 1,
        index: this.imageList.length,
        id: this.guid(),
        disable: false,
        offset: 0,
        moveEnd: false
      })
      this.add.x = (this.imageList.length % this.colsValue) * this.viewWidth + 'px'
      this.add.y = Math.floor(this.imageList.length / this.colsValue) * this.viewWidth + 'px'
      this.sortList()
    },
    nothing(){},
    rpx2px(v){
      return this.width * v / 750
    },
    guid() {
    	function S4() {
    		return (((1 + Math.random()) * 0x10000) | 0).toString(16).substring(1);
    	}
    	return (S4() + S4() + "-" + S4() + "-" + S4() + "-" + S4() + "-" + S4() + S4() + S4());
    }
  }
}
</script>

<style lang="scss" scoped>
.con {
  padding: 30rpx;
  .area {
    width: 100%;
    .view {
      display: flex;
      justify-content: center;
      align-items: center;
      .area-con {
        position: relative;
        .pre-image {
          width: 100%;
          height: 100%;
        }
        .del-con {
          position: absolute;
          top: 0rpx;
          right: 0rpx;
          padding: 0 0 20rpx 20rpx;
          .del-wrap {
            width: 36rpx;
            height: 36rpx;
            background-color: rgba(0, 0, 0, 0.4);
            border-radius: 0 0 0 10rpx;
            display: flex;
            justify-content: center;
            align-items: center;
            .del-image {
              width: 20rpx;
              height: 20rpx;
            }
          }
        }
      }
    }
    .add {
      position: absolute;
      display: flex;
      justify-content: center;
      align-items: center;
      .add-wrap{
        display: flex;
        justify-content: center;
        align-items: center;
        background-color: #eeeeee;
      }
    }
  }
}
</style>