# 图片拖拽排序插件
## shmily-drag-image
实际效果建议下载示例项目看看
[点我快速预览 Web 版](http://static-11ea0c21-6b8f-47f7-b77f-cb0c7ea3f355.bspapp.com/shmily-drag-image/)
## 使用方式：
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
<shmily-drag-image :list="list"></shmily-drag-image>
```
---


属性名 | 类型 | 默认值 | 说明
:-:|:-:|:-:|---
list | Array | [] | 排序后图片列表
number | Number | 6 | 图片数量限制
imageWidth | Number | 230 | 图片父容器宽度（实际显示的图片宽度为 imageWidth / 1.1 ），单位 rpx