<template>
	<view style="height: 150vh;">
		<shmily-image-drag :list.sync="list" :number="6" :imageWidth="230" :cols="3" :padding="10" :scale="1.1"></shmily-image-drag>
	</view>
</template>

<script>
  import shmilyImageDrag from '@/components/shmily-drag-image/shmily-drag-image.vue'
	export default {
    components:{
      shmilyImageDrag
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
