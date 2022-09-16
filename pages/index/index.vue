<template>
  <view style="background-color: #2e2e2e;min-height: 100vh;width: 100vw;">
    <!-- 基本使用 -->
    <shmily-drag-image v-model="list"></shmily-drag-image>

    <!-- 限定图片宽度 -->
    <!-- <shmily-drag-image v-model="list" :imageWidth="224"></shmily-drag-image> -->

    <!-- 限定列数 -->
    <!-- <shmily-drag-image v-model="list" :cols="3"></shmily-drag-image> -->

    <!-- 自定义添加、删除 -->
    <!-- <shmily-drag-image v-model="list" :addImage="addImage" :delImage="delImage"></shmily-drag-image> -->
    
    <!-- 对象数组 -->
    <!-- <shmily-drag-image v-model="lists" keyName="src" :addImage="addImages" :delImage="delImage"></shmily-drag-image> -->
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 排序后的图片列表
        list: [
          'http://static-11ea0c21-6b8f-47f7-b77f-cb0c7ea3f355.bspapp.com/shmily-drag-image/static/1.jpg',
          'http://static-11ea0c21-6b8f-47f7-b77f-cb0c7ea3f355.bspapp.com/shmily-drag-image/static/2.jpg',
          'http://static-11ea0c21-6b8f-47f7-b77f-cb0c7ea3f355.bspapp.com/shmily-drag-image/static/3.jpg',
        ],
        lists: [
          {
            src: 'http://static-11ea0c21-6b8f-47f7-b77f-cb0c7ea3f355.bspapp.com/shmily-drag-image/static/1.jpg',
          },
          {
            src: 'http://static-11ea0c21-6b8f-47f7-b77f-cb0c7ea3f355.bspapp.com/shmily-drag-image/static/2.jpg',
          },
          {
            src: 'http://static-11ea0c21-6b8f-47f7-b77f-cb0c7ea3f355.bspapp.com/shmily-drag-image/static/3.jpg',
          },
        ],
      }
    },
    methods: {
      addImage() {
        console.log('addImage');
        // 将图片地址添加到图片列表
        this.list.push('http://static-11ea0c21-6b8f-47f7-b77f-cb0c7ea3f355.bspapp.com/shmily-drag-image/static/4.jpg')
      },
      addImages() {
        console.log('addImages');
        // 将图片地址添加到图片列表
        this.lists.push({
          id: 4,
          src: 'http://static-11ea0c21-6b8f-47f7-b77f-cb0c7ea3f355.bspapp.com/shmily-drag-image/static/4.jpg',
        })
      },
      delImage(done) {
        console.log('delImage');
        uni.showModal({
          content: '是否删除?',
          success: (res) => {
            if(res.confirm) {
              // 执行 done() 删除
              done()
            }
          }
        })
      },
    }
  }
</script>

<style>

</style>
