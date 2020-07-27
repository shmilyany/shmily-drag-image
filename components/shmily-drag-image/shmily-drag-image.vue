<template>
  <view class="con">
    <movable-area class="area" :style="{ height: areaHeight }">
      <block v-for="(item, index) in imageList" :key="item.id">
        <movable-view
          class="view"
          :x="item.x"
          :y="item.y"
          direction="all"
          @change="onChange($event, item)"
          @touchstart="touchstart(item)"
          @mousedown="touchstart(item)"
          @touchend="touchend(item)"
          @mouseup="touchend(item)"
          
          :style="{ width: imageWidth + 'rpx', height: imageWidth + 'rpx', 'z-index': item.zIndex, opacity: item.opacity }"
        >
          <view class="area-con" :style="{ width: viewWidth, height: viewWidth, transform: 'scale(' + item.scale + ')' }">
            <image class="pre-image" :src="item.src" mode="aspectFill"></image>
            <view class="del-con" @click="delImage(item, index)" @touchstart.stop="delImage(item, index)">
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
      <!-- 添加 -->
      <view
        class="add"
        v-if="imageList.length < number"
        :style="{ top: add.y, left: add.x, width: viewWidth, height: viewWidth, margin: imageWidth / 22 + 'rpx' }"
        @click="addimage"
      >
        <image
          style="width: 54rpx;height: 54rpx;"
          src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADYAAAA2CAYAAACMRWrdAAABIUlEQVRoQ+2a2w2DMAxFeQzWrsMUbadAsEw3S1CqVgppKwLX8BEOP4iHTXx8uUgWdVXoVhdaV0VhSmf7vr/H8V3XzY6V3P9iD+nYOI5P7/01LMI596AwoZV0TIBXIUWFXhKLFBWYSFGhhxQN6SFFQ5i4ogITKSr0cEVDekjRECauqMBEigq9U7piOk2yAti27SUe5ljlTfPEQ6KZecTvwl4P3ytvOv06R2HDMNzes7+6aRrvnHvtf50L13Lp50rx88zcvNlS3JpwKQ67XyK04nq2nFbk/LqVjin0TvmBNgQ2S4UUDcliHgpMpKjQwxUN6SFFQ5i4ogITKSr0cEVDekjRECauqMAsVoph+hVPtYr5+03p9tbYQ96xrYtT4ootbAJGVxxVTapVswAAAABJRU5ErkJggg=="
        ></image>
      </view>
    </movable-area>
  </view>
</template>

<script>
export default {
  data() {
    return {
      imageList: [],
      itemWidth: 0,
      add: {
        x: 0,
        y: 0
      },
      cols: 0
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
    }
  },
  computed: {
    areaHeight() {
      if (this.imageList.length < this.number) {
        return Math.ceil((this.imageList.length + 1) / this.cols) * this.imageWidth + 'rpx'
      } else {
        return (Math.ceil((this.imageList.length + 1) / this.cols) - 1) * this.imageWidth + 'rpx'
      }
    },
    viewWidth() {
      return this.imageWidth / 1.1 + 'rpx'
    }
  },
  mounted() {
    const query = uni.createSelectorQuery().in(this)
    query.select('.add').boundingClientRect(data => {
      this.itemWidth = data.width
    })
    query.select('.con').boundingClientRect(data => {
      this.cols = Math.floor(data.width / this.itemWidth)
    })
    query.exec()
  },
  methods: {
    onChange(e, item) {
      if(!item) return
      item.oldX = e.detail.x
      item.oldY = e.detail.y
      if (e.detail.source === 'touch') {
        let x = Math.floor((e.detail.x + this.itemWidth / 2) / this.itemWidth)
        let y = Math.floor((e.detail.y + this.itemWidth / 2) / this.itemWidth)
        let index = this.cols * y + x
        if (item.index != index && index < this.imageList.length) {
          for (let obj of this.imageList) {
            if (item.index > index && obj.index >= index && obj.index < item.index) {
              obj.index += 1
              obj.x = obj.oldX
              obj.y = obj.oldY
              obj.absX = obj.index % this.cols
              obj.absY = Math.floor(obj.index / this.cols)
              this.$nextTick(() => {
                obj.x = obj.absX * this.imageWidth + 'rpx'
                obj.y = obj.absY * this.imageWidth + 'rpx'
              })
            }
            if (item.index < index && obj.index <= index && obj.index > item.index) {
              obj.index -= 1
              obj.x = obj.oldX
              obj.y = obj.oldY
              obj.absX = obj.index % this.cols
              obj.absY = Math.floor(obj.index / this.cols)
              this.$nextTick(() => {
                obj.x = obj.absX * this.imageWidth + 'rpx'
                obj.y = obj.absY * this.imageWidth + 'rpx'
              })
            }
          }
          item.index = index
          item.absX = x
          item.absY = y
          this.sortList()
        }
      }
    },
    touchstart(item) {
      this.imageList.forEach(v => {
        v.zIndex = v.index
      })
      item.zIndex = 99
      item.scale = 1.1
      item.opacity = 0.7
    },
    touchend(item) {
      item.scale = 1
      item.opacity = 1
      item.x = item.oldX
      item.y = item.oldY
      this.$nextTick(() => {
        item.x = item.absX * this.imageWidth + 'rpx'
        item.y = item.absY * this.imageWidth + 'rpx'
      })
    },
    addimage() {
      let checkNumber = this.number - this.imageList.length
      uni.chooseImage({
        count: checkNumber,
        sourceType: ['album', 'camera'],
        success: res => {
          for (let i = 0; i < checkNumber; i++) {
            let absX = this.imageList.length % this.cols
            let absY = Math.floor(this.imageList.length / this.cols)
            let x = absX * this.imageWidth + 'rpx'
            let y = absY * this.imageWidth + 'rpx'

            this.imageList.push({
              src: res.tempFilePaths[i],
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
              id: this.guid()
            })

            this.add.x = (this.imageList.length % this.cols) * this.imageWidth + 'rpx'
            this.add.y = Math.floor(this.imageList.length / this.cols) * this.imageWidth + 'rpx'

            this.sortList()
          }
        }
      })
    },
    delImage(item, index){
      this.imageList.splice(index, 1)
      for (let obj of this.imageList) {
        if (obj.index > item.index) {
          obj.index -= 1
          obj.x = obj.oldX
          obj.y = obj.oldY
          obj.absX = obj.index % this.cols
          obj.absY = Math.floor(obj.index / this.cols)
          this.$nextTick(() => {
            obj.x = obj.absX * this.imageWidth + 'rpx'
            obj.y = obj.absY * this.imageWidth + 'rpx'
          })
        }
      }
      this.add.x = (this.imageList.length % this.cols) * this.imageWidth + 'rpx'
      this.add.y = Math.floor(this.imageList.length / this.cols) * this.imageWidth + 'rpx'
      this.sortList()
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
    nothing(){},
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
  margin: 30rpx;
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
      background-color: #eeeeee;
    }
  }
}
</style>
