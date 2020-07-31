# 图片拖拽排序插件
## shmily-drag-image

> 点击预览、长按拖拽排序、初始时可添加图片、自定义添加图片

实际效果建议下载示例项目看看
[点我快速预览 Web 版](http://static-11ea0c21-6b8f-47f7-b77f-cb0c7ea3f355.bspapp.com/shmily-drag-image/)

---

[div拖动排序（github）](https://github.com/shmilyany/shmily-drag-image/tree/shmily-drag-view)

---
## 基本使用：
### 在 script 中引用组件
```
import shmilyDragImage from '@/components/shmily-drag-image/shmily-drag-image.vue'
export default {
  components: {
    shmilyDragImage
  },
  data() {
    return {
      list: []
    }
  }
}
```
### 在 template 中使用组件
```
<shmily-drag-image :list.sync="list"></shmily-drag-image>
```
---
## 自定义添加：
### 在 script 中引用组件
```
import shmilyDragImage from '@/components/shmily-drag-image/shmily-drag-image.vue'
export default {
  components:{
    shmilyDragImage
  },
  data() {
    return {
      list: []
    }
  },
  methods:{
    addImage(){
      // 打开第三方图片编辑插件
      // ...
      
      // 将编辑后图片地址添加到图片列表
      this.$refs.dragImage.addImage('')
    }
  }
}
```
### 在 template 中使用组件
```
<shmily-drag-image ref="dragImage" :list.sync="list" :custom="true" @addImage="addImage"></shmily-drag-image>
```
---

## 属性说明

属性名 | 类型 | 默认值 | 说明
:-:|:-:|:-:|---
list | Array | [ ] | 排序后图片列表（初始时可添加图片）
number | Number | 6 | 图片数量限制
imageWidth | Number | 230 | 图片父容器宽度（实际显示的图片宽度为 imageWidth / 1.1 ），单位 rpx
cols | Number | 0 | 图片列数（cols > 0 则 imageWidth 无效）
padding | Number | 10 | 图片周围空白填充，单位 rpx
scale | Number | 1.1 | 拖动图片时放大倍数 [0, ∞)
opacity | Number | 0.7 | 拖动图片时不透明度
custom | Boolean | false | 自定义添加（需配合 @addImage 事件使用）

---

## 事件说明

方法名称 | 说明
:-: | ---
@addImage | 可在此方法内打开图片编辑等插件

## 方法说明

方法名称 | 说明
:-: | ---
addImage(String) | 通过 ref 使用，添加编辑后图片，只有 custom: true 下生效