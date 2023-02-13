<template>
  <div class="forum">
		<AdItem unitId="adunit-b819125dace4643b"></AdItem>
    <navigator class="list-item" v-for="(item, index) in postData" :key="index" :url="`/pages/detail/index?id=${index + 1}`">
      <div class="title">
        {{item.title}}
      </div>
      <div class="image-box">
        <img class="img" :src="item.path + '' +item.images[0]" lazy-load="true" mode="aspectFill" />
      </div>
    </navigator>
		<div class="empty" v-if="!(postData && postData.length)">
			暂无数据
		</div>
  </div>
</template>

<script>
import AdItem from '@/components/mainAdItem.vue'
const baseUrl = 'https://jihulab.com/blank1895/emoji-wallpapers/-/raw/master/'

export default {
  data () {
    return {
      postLists: []
    }
  },
	components: {
		AdItem
	},
  computed: {
    postData () {
      return this.postLists && this.postLists.map(item => ({
        ...item,
        path: baseUrl
      })) || []
    }
  },
  components: {},
  created () {
    // this.$cookie.set('test', 'hello world', 1)
    // console.log(Vue)
  },
	onLoad(){
		
	},
  mounted () {
    this.getPostsList()
    wx.showShareMenu({
      withShareTicket: true,
      menus: ['shareAppMessage', 'shareTimeline']
    })
  },
  methods: {
    getPostsList () {
      let _this = this
      wx.request({
        url: 'https://jihulab.com/blank1895/emoji-wallpapers/-/raw/master/lists.json?v=' + new Date().getTime(),
        data: {},
        method: 'GET',
        header: {
          'content-type': 'application/json'
        },
        success (res) {
          _this.postLists = res.data.forum || []
        },
        error (err) {
          console.log('error:::', err)
        }
      })
    },
    toDetail (index) {
      return `/pages/detail/main?id=${index}`
    }
  }
}
</script>

<style lang="scss" scoped>
.forum{
  .list-item{
		margin-bottom: 16px;
		padding: 8px 8px;
		box-shadow: 0 0 5px 2px rgba(0, 0, 0, 0.2);
    .title{
      font-size: 16px;
      font-weight: bold;
      color: #3A404E;
    }
    .image-box{
      margin-top: 8px;
      width: 100%;
      height: 240px;
      .img{
        width: 100%;
				height: 100%;
        border-radius: 4px;
      }
    }
  }
	.empty{
		margin: 20px auto;
		font-size: 16px;
		text-align: center;
		color: #FF88AF;
	}
}
</style>
