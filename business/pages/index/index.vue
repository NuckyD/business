<template>
	<view class="content">
		<block v-for="(item,index) in shop" :key="index">
			<view class="content-view">
				<view class="content-img">
					<image :src="item.wholedata.Coverimg" mode="aspectFill"></image>
				</view>
				<view class="content-title">
					<text>{{item.wholedata.title}}</text>
					<text>{{item.wholedata.describe}}</text>
				</view>
			</view>
		</block>
		<!-- 审核状态 -->
		<stateing ref="mon" v-if="shopif"></stateing>
	</view>
</template>

<script> 
	// 引入审核组件
	import stateing from '../../element/stateing.vue'
	var db = wx.cloud.database()
	var commodity = db.collection('commodity')
	export default {
		components:{
			stateing
		},
		data() {
			return {
				shopif:true,
				shop:[]
			}
		},
		methods:{
			
			// 用户是否已认证
			ifUser(){
				commodity.get()
				.then((res)=>{
					console.log(res)
					var username = res.data[0]
					if(res.data.length === 0){
						//console.log('没有认证')
						this.shopif = true
						let staimg = '../static/img/noimage.png'
						let title = '你还没有发布商品'
						this.compstate(staimg,title)
					} else {
						this.shopif = false
						this.shop = res.data
					}
				})
				.catch((err)=>{
					console.log(err)
				})
			},
			
			// 被调用的审核状态
			compstate(staimg,title){
				this.$nextTick(()=>{   //dom更新循环结束之后的延迟回调
					this.$refs.mon.init(staimg,title)
				})
			}
		},
		onShow() {
			this.ifUser()
		}
	}
</script>

<style scoped>
	.content{margin: 10upx;}
	text{display: block;}
	.content-view{display: flex; justify-content: space-between;
	height: 200upx !important; overflow: hidden;
	border-bottom: 1rpx solid #E4E8EB; padding-bottom: 5upx;
	margin-bottom: 20upx;
	}
	.content-img{width: 300upx !important; height: 200upx !important;
	}
	.content-img image{width: 100%; height: 100%; border-radius: 10upx;}
	.content-title{
	width: 100%;
	padding-left: 10upx;
	}
	.content-title text:nth-child(1){
		font-size: 30upx;
		font-weight: bold;
		display: -webkit-box;
		-webkit-box-orient: vertical;
		-webkit-line-clamp: 1;
		overflow: hidden;
	}
	.content-title text:nth-child(2){
		font-size: 28upx;
		display: -webkit-box;
		-webkit-box-orient: vertical;
		-webkit-line-clamp: 2;
		overflow: hidden;
		padding-top: 20upx;
	}
</style>
