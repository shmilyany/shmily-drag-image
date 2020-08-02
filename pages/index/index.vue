<template>
	<view>
    <!-- 基本使用 -->
		<shmily-drag-image :list.sync="list"></shmily-drag-image>
    
    <!-- 限定图片宽度 -->
    <!-- <shmily-drag-image :list.sync="list" :imageWidth="224"></shmily-drag-image> -->
    
    <!-- 限定列数 -->
		<!-- <shmily-drag-image :list.sync="list" :cols="3"></shmily-drag-image> -->
    
    <!-- 自定义添加 -->
		<!-- <shmily-drag-image ref="dragImage" :list.sync="list" :custom="true" @addImage="addImage"></shmily-drag-image> -->
	</view>
</template>

<script>
  import shmilyDragImage from '@/components/shmily-drag-image/shmily-drag-image.vue'
	export default {
    components:{
      shmilyDragImage
    },
		data() {
			return {
        // 排序后的图片列表
				list: [
          'http://static-11ea0c21-6b8f-47f7-b77f-cb0c7ea3f355.bspapp.com/shmily-drag-image/static/5.jpg',
          'http://static-11ea0c21-6b8f-47f7-b77f-cb0c7ea3f355.bspapp.com/shmily-drag-image/static/2.jpg',
          'http://static-11ea0c21-6b8f-47f7-b77f-cb0c7ea3f355.bspapp.com/shmily-drag-image/static/3.jpg',
          ]
			}
		},
    methods:{
      addImage(){
        // 打开图片裁剪
        // ...
        
        // 将裁剪后图片地址添加到图片列表
        this.$refs.dragImage.addImage('http://static-11ea0c21-6b8f-47f7-b77f-cb0c7ea3f355.bspapp.com/shmily-drag-image/static/4.jpg')
      }
    }
	}
</script>

<style>
	
</style>
