<template>
	<view>
		<view class="authent" v-if="pageauth">
			<view>
				<text>商品标题</text>
				<input type="text" placeholder="请输入商品标题" v-model="title"/>
			</view>
			<!-- 商品描述 -->
			<view>
				<text>商品描述</text>
				<input type="text" placeholder="请输入商品描述" v-model="describe"/>
			</view>
			<!-- 商品标签 -->
			<view>
				<text>商品标签</text>
				<input type="text" placeholder="请输入商品标签" v-model="label"/>
			</view>
			<!-- 商品类型 -->
			<view>
				<text>商品类型</text>
				<input type="text" placeholder="请输入商品类型" v-model="typedata"/>
			</view>
			<!-- 商品价格 -->
			<view>
				<text>商品价格</text>
				<input type="number" placeholder="请输入商品价格" v-model="price"/>
			</view>
			<!-- 出发地 -->
			<view>
				<text>出发地</text>
				<view class="city-list">
				<input type="text"  placeholder="客户从哪些城市可以出发" v-model="citys"/>
				<view @click="Determine()">确定</view>
				</view>
			</view>
			<view class="menu-block">
				<block v-for="(item,index) in setdata" :key="index">
					<view>
						<text>{{item}}</text>
						<image src="../../static/img/guanbi.svg" mode="widthFix" @click="delecity(index)"></image>
					</view>
				</block>
			</view>
			<!-- 目的地 -->
			<view>
				<text>到达目的地</text>
				<input type="text" placeholder="旅游目的地" v-model="destination"/>
			</view>
			<!-- 上传封面图 -->
			<view>
				<text>上传商品封面图(建议尺寸800*800像素)</text>
				<!-- 上传图片 -->
				<view class="travels-image">
					<view class="topimg">
						<image src="../../static/img/topimg.png" mode="widthFix" @click="uploadImg(fengmian,manyfeng)"></image>
					</view>
					<!-- 上传封面 -->
					<view class="conteng">
						<block v-for="(item,index) in Coverimg" :key="index">
							<view  class="conteng-img">
							<image :src="item" mode="aspectFill" class="uploadimg"></image>
							<image src="../../static/img/deteimg.svg" mode="widthFix" class="deleteimg" @click="deleteImg(index,fengmian)"></image>
							</view>
						</block>
					</view>
				</view>
			</view>
			
			<!-- 上传轮播图 -->
			<view>
				<text>上传商品轮播图(建议尺寸750*500像素)</text>
				<!-- 上传图片 -->
				<view class="travels-image">
					<view class="topimg">
						<image src="../../static/img/topimg.png" mode="widthFix" @click="uploadImg(lunbo,manylun)"></image>
					</view>
					<!-- 上传的图片 -->
					<view class="conteng">
						<block v-for="(item,index) in Banner" :key="index">
							<view  class="conteng-img">
							<image :src="item" mode="aspectFill" class="uploadimg"></image>
							<image src="../../static/img/deteimg.svg" mode="widthFix" class="deleteimg" @click="deleteImg(index,lunbo)"></image>
							</view>
						</block>
					</view>
				</view>
			</view>
			
			<!-- 上传图文详情介绍 -->
			<view>
				<text>上传商品图文详情介绍</text>
				<!-- 上传图片 -->
				<view class="travels-image">
					<view class="topimg">
						<image src="../../static/img/topimg.png" mode="widthFix" @click="uploadImg(xiangqing,manylun)"></image>
					</view>
					<!-- 上传的图片 -->
					<view class="conteng">
						<block v-for="(item,index) in Details" :key="index">
							<view  class="conteng-img">
							<image :src="item" mode="aspectFill" class="uploadimg"></image>
							<image src="../../static/img/deteimg.svg" mode="widthFix" class="deleteimg" @click="deleteImg(index,xiangqing)"></image>
							</view>
						</block>
					</view>
				</view>
			</view>
			
			<!-- 距离 -->
			<view class="distance"></view>
			
			<!-- 提交 -->
			<view class="release" :class="{ active: isActive }" @click="flagg && suBmitd()">
			提交
			</view>
			
			<!-- 提示用户上传成功与否 -->
			<view class="warp" v-if="relend">
				<view class="warp-view tipmin">
					<text>{{reldata}}</text>
				</view>
			</view>
			
		</view>
		
		<stateing ref="mon" v-if="!pageauth"></stateing>
		
	</view>
</template>

<script>
	//上传图片到本地
	import {uploadimage} from '../../common/list.js'
	// 引入审核组件
	import stateing from '../../element/stateing.vue'
	//上传图片到运存储
	import {uploads} from '../../common/uploads.js'
	var db = wx.cloud.database()
	var authentication = db.collection('authentication')
	// 上传静态资源的云存储文件
	var resou = 'commodity'
	export default{
		components:{
			stateing
		},
		data() {
			return {
				pageauth:false,
				flagg:false,
				isActive:false,
				relend:false,
				reldata:'正在提交...请勿关闭该页面',
				citys:'',
				// 提交的数据
				title:'',
				describe:'',
				label:'',
				typedata:'',
				price:'',
				setdata: [],
				destination:'',
				Coverimg:[],
				Banner:[],
				Details:[],
				// 取到商家信息
				enterprise:'',
				logoimg:'',
				fengmian:'fengmian',
				manyfeng:1,
				lunbo:'lunbo',
				manylun:9,
				xiangqing:'xiangqing',
				// 上传的得到的封面图，轮播图，商品详情fileid
				uploadcov:'',
				uploadbanner:[],
				uploaddetails:[]
			}
		},
		methods:{
			
			// 用户是否已认证
			ifUser(){
				authentication.get()
				.then((res)=>{
					// console.log(res)
					var username = res.data[0]
					if(res.data.length == 0 || username.examine != 'Success'){
						// 没有认证
						//console.log('没有认证')
						this.pageauth = false
						let staimg = '../static/img/tishidat.svg'
						let title = '商家认证完成后才可以发布商品哦'
						this.compstate(staimg,title)
					}else if(username.examine == 'Success'){
						//console.log('已认证')
						this.pageauth = true
						// 取到商家logo和企业名称
						let releuser = res.data[0].storeDetail
						//console.log(releuser)
						this.enterprise = releuser.enterprise
						this.logoimg = releuser.logoimg
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
			},
			// 出发地
			Determine(){
				if(this.citys != ''){
					this.setdata.push(this.citys)
					let newarr = Array.from(new Set(this.setdata))
					this.setdata = newarr
				}
			},
			// 删除城市
			delecity(index){
				this.setdata.splice(index, 1)
			},
			// 上传图片
			uploadImg(imgs,many){
				console.log(imgs)
				uploadimage(many)
				.then((res)=>{
					console.log(res)
					if(imgs == 'fengmian'){
						// 封面
						this.Coverimg = res
					}else if(imgs == 'lunbo'){
						// 轮播
						this.Banner.push(...res)
					}else if(imgs == 'xiangqing'){
						this.Details.push(...res)
					}
					
				})
				.catch((err)=>{
					console.log(err)
				})
			},
			
			// 删除图片
			deleteImg(index,deleteis){
				if(deleteis == 'fengmian'){
					// 删除封面
					this.Coverimg.splice(index, 1)
				}else if(deleteis == 'lunbo'){
					// 删除轮播
					this.Banner.splice(index, 1)
				}else if(deleteis == 'xiangqing'){
					// 删除详情
					this.Details.splice(index, 1)
				}
			},
			
			// 提交
			suBmitd(){
				console.log('提交')
				this.relend = true
				this.usercommod()
			},
			
			// 上传商品数据
			async usercommod(){
				// 等待上传封面图
				let coverimgdata = await this.covfun()
				console.log(coverimgdata)
				this.uploadcov = coverimgdata
				// 等待上传商品轮播图
				let bannerimgdata = await this.bannerfun()
				console.log(bannerimgdata)
				this.uploadbanner = bannerimgdata
				// 等待上传商品图文介绍
				let detailsimgdata = await this.detailsfun()
				console.log(detailsimgdata)
				this.uploaddetails = detailsimgdata
				// 上传所有数据到数据库
				await this.wholefun()
				
			},
			
			// 等待上传封面图到云存储
			covfun(){
				return new Promise((resolve,reject)=>{
					uploads(this.Coverimg,resou)
					.then((res)=>{
						// console.log(res)
						resolve(...res)
					})
					.catch((err)=>{
						console.log(err)
						reject(err)
					})
				})
			},
			
			// 等待上传商品轮播图到云存储
			bannerfun(){
				return new Promise((resolve,reject)=>{
					uploads(this.Banner,resou)
					.then((res)=>{
						resolve(res.reverse())
					})
					.catch((err)=>{
						console.log(err)
						reject(err)
					})
				})
			},
			
			// 等待上传商品图文介绍
			detailsfun(){
				return new Promise((resolve,reject)=>{
					uploads(this.Details,resou)
					.then((res)=>{
						resolve(res.reverse())
					})
					.catch((err)=>{
						console.log(err)
						reject(err)
					})
				})
			},
			
			// 上传所有数据到数据库
			wholefun(){
				// 以对象形式上传
				let wholedata = {
					enterprise:this.enterprise,
					logoimg:this.logoimg,
					title:this.title,
					describe:this.describe,
					label:this.label,
					typedata:this.typedata,
					price:Number(this.price),
					setdata: this.setdata,
					destination:this.destination,
					Coverimg:this.uploadcov,
					Details:this.uploaddetails,
					Banner:this.uploadbanner,
				}
				db.collection('commodity').add({
				  // data 字段表示需新增的 JSON 数据
				  data: {
					wholedata:wholedata
				  }
				})
				.then((res)=>{
					console.log(res)
					// 全部提交成功，清除更新提示
					this.reldata = '发布商品成功'
					this.title = ''
					this.describe = ''
					this.label = ''
					this.typedata = ''
					this.price = ''
					this.setdata = ''
					this.destination = ''
					this.Coverimg.length = ''
					this.Banner = ''
					this.Details = ''
					this.citys = ''
					setTimeout(()=>{
						this.relend = false
					},1500)
				})
				.catch((err)=>{
					console.log(err)
				})
			}
		},
		
		// 商家是否已认证
		onShow() {
			this.ifUser()
		},
		
		// 判断
		computed:{
			testdata(){
				if(this.title !='' && this.describe != '' && this.label != '' && this.typedata != '' && this.price != '' && this.setdata.length != 0 && this.destination != '' && this.Coverimg.length != 0 && this.Banner.length != 0 && this.Details.length != 0){
					console.log('不空')
					this.isActive = true
					this.flagg = true
				}else{
					console.log('空')
					this.isActive = false
					this.flagg = false
				}
			}
		}
	}
</script>

<style scoped>
	@import "../../common/uni.css";
	.menu-block view {
			background: #ffd300;
			border-radius: 6upx;
			text-align: center;
			padding: 5upx 30upx;
			margin: 10upx 15upx 15upx 0;
			position: relative;
		}
	
	.menu-block image{width: 25upx; height: 25upx;
	position: absolute; top: 5upx; right: 5upx;}	
	.menu-block text{font-size: 27upx;
			color: #292c33;}
		
	.menu-block {
		display: flex;
		flex-direction: row;
		justify-content: flex-start;
		flex-wrap: wrap;
	}
	.city-list{display: flex; align-items: center; justify-content: space-between;}
	.city-list input{width: 100%;}
	.city-list view{background: #4CD964; font-size: 30upx; width: 150upx;
	height: 73upx; text-align: center; line-height: 73upx;
	border-radius: 6upx;
	margin-left: 10upx;}
	/* 九宫格 */
	.deleteimg{width: 38upx; height: 38upx;
	position: absolute;
	top: 6upx;
	right: 6upx;}
</style>
