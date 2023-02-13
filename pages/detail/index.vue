<template>
  <div class="detail-box">
    <div class="item" >
      <div class="title">
				{{detail.title || ''}}
      </div>
      <div class="content">
				<div class="txt">
					{{detail.content || ''}}
				</div>
        <div class="images-box" v-if="detail && detail.images">
          <img class="img-item" v-for="(img, index) in detail.images" :key="index" mode="aspectFit" lazy-load="true" :src="fetchImgUrl(img)" />
        </div>
				<div class="attachment" v-if="showAttachment">
					{{detail.attachment}}
				</div>
				<div v-else class="ad-box">
					<div class="ad-tips">
						<div class="tips-txt">
							1，点击下方，<b>【立即解锁，查看更多美图】</b>按钮，等待视频播放完毕
						</div>
						<div class="tips-txt">
							2，观看完视频后，点击视频页面<b>右上角关闭按钮</b>，可解锁隐藏内容
						</div>
					</div>
					<div class="button-box">
						<button @click="showAd" class="ad-button">立即解锁，查看更多美女</button>
					</div>
				</div>
      </div>
			<div class="banner-ad">
				<ad unit-id="adunit-9febccf65acb2006" ad-type="video" ad-theme="white"></ad>
			</div>
    </div>
  </div>
</template>

<script>
import {baseUrl} from '@/utils/index.js'
export default {
  data () {
    return {
			detail: null,
			id: 0,
			rewardedVideoAd: null,
			showAttachment: false
    }
  },
  computed: {
		handleDetail(){
			console.log('====', this.detail)
			return this.detail
		}
	},
  components: {},
  created () {},
	onLoad() {
		const page = getCurrentPages()[0]
		const {options, route} = page
		this.id = options.id
		
		// 激励广告
		if(wx.createRewardedVideoAd){
			this.rewardedVideoAd = wx.createRewardedVideoAd({ adUnitId: 'adunit-6ad070fced0eea91' })
			this.rewardedVideoAd.onLoad(() => {
				console.log('onLoad event emit')
			})
			this.rewardedVideoAd.onError((err) => {
				console.log('onError event emit', err)
			})
			this.rewardedVideoAd.onClose((res) => {
				console.log('onClose event emit', res)
				if(res.isEnded) {
					this.showAttachment = true
				}
			})
		}
	},
  mounted () {
    this.fetchDetails(this.id)
    wx.showShareMenu({
      withShareTicket: true,
      menus: ['shareAppMessage', 'shareTimeline']
    })
  },
  methods: {
    fetchDetails (id) {
			wx.showLoading({ title: '加载中...', mask: true })
      let _this = this
      wx.request({
        url: `${baseUrl}/lists.json?v=${new Date().getTime()}`,
        data: {},
        method: 'GET',
        header: {
          'content-type': 'application/json'
        },
        success (res) {
					_this.detail = res.data && res.data.forum.find(item => {
						return item.id == id
					})
					wx.hideLoading()
        },
        error (err) {
					wx.hideLoading()
          console.log('error:::', err)
        }
      })
    }, 
		fetchImgUrl(path) {
			return `${baseUrl}${path}`
		},
		showAd(){
			this.rewardedVideoAd.show()
		}
  }
}
</script>

<style lang="scss">
.detail-box {
  .item {
    // background: #eee;
		padding: 8px;
    .title{
			position: relative;
      width: 100%;
      line-height: 32px;
			font-size: 16px;
			color: #FF88AF;
			border-bottom: 1px solid #FF88AF;
    }
		.content{
			padding: 0 8px;
			margin: 8px 0;
			.txt{
				font-size: 14px;
				color: #3A404E;
			}
		}
    .images-box {
      position: relative;
      width: 100%;
			margin: 8px 0;
      box-sizing: border-box;
      .img-item{
				margin: 8px 0;
        width: 100%;
        border-radius: 5px;
        box-shadow: 0px 0 5px rgba(0, 0, 0, .2);
				background: #eeeeee;
      }
      .banner-ad{
        padding: 5px;
        background: #FF88AF;
      }
    }
		.attachment{
			padding: 8px 0;
			font-size: 14px;
			color: #FF88AF;
		}
		.ad-box{
			position: relative;
			margin: 8px 0 16px;
			width: 100%;
			.ad-tips{
				top: 0;
				font-size: 14px;
				margin: 16px 0;
				b{
					display: inline-block;
					color: #FF88AF;
					font-weight: bold;
				}
			}
			.button-box{
				position: relative;
				&::before{
					content: '';
					display: block;
					width:100%;
					height:120px;
					filter: blur(5px);
					background: #FF88AF url('https://jihulab.com/blank1895/emoji-wallpapers/-/raw/master/images/background.jpg') no-repeat center bottom;
					background-size: 100% auto;
				}
				.ad-button{
					position: absolute;
					top: 50%;
					left: 50%;
					transform: translate(-50%, -50%);
					width: 60%;
					font-size: 14px;
					font-weight: bold;
					color: #FF88AF;
					background: #f1f1f1;
				}
			}
			
		}
  }
}
</style>
