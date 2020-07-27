<template>
	<view>
		<shmily-image-drag :list.sync="list" :number="6" imageWidth="230"></shmily-image-drag>
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
				list: []
			}
		}
	}
</script>

<style>
	
</style>
