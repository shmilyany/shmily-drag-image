<template>
	<view>
		<shmily-drag-image :list.sync="list"></shmily-drag-image>
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
				list: ['/static/1.jpg','/static/2.jpg','/static/3.jpg']
			}
		}
	}
</script>

<style>
	
</style>
