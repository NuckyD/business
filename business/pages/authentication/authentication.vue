<template>
	<view>
		<view v-if="pageauth">
			<!-- 企业资质信息 -->
			<view class="authent">
				<view class="title">企业资质信息</view>
				<view>
					<text>企业全称</text>
					<input type="text" placeholder="请输入企业全称" v-model="enterprise"/>
				</view>
				<!-- 上传营业执照 -->
				<view>
					<text>上传工商营业执照</text>
					<!-- 上传图片 -->
					<view class="travels-image">
						<view class="topimg">
							<image src="../../static/img/topimg.png" mode="widthFix" @click="uploadImg(top)"></image>
						</view>
						<!-- 上传的图片 -->
						<view class="conteng">
							<block v-for="(item,index) in topimg" :key="index">
								<view  class="conteng-img">
								<image :src="item" mode="aspectFill" class="uploadimg"></image>
								</view>
							</block>
						</view>
					</view>
				</view>
				<!-- 上传店铺logo -->
				<view>
					<text>上传店铺logo</text>
					<!-- 上传图片 -->
					<view class="travels-image">
						<view class="topimg">
							<image src="../../static/img/topimg.png" mode="widthFix" @click="uploadImg(logo)"></image>
						</view>
						<!-- 上传的图片 -->
						<view class="conteng">
							<block v-for="(item,index) in logoimg" :key="index">
								<view  class="conteng-img">
								<image :src="item" mode="aspectFill" class="uploadimg"></image>
								</view>
							</block>
						</view>
					</view>
				</view>
			</view>
			
			<!-- 对公账户信息 -->
			<view class="authent">
				<view class="title">对公账户信息</view>
				<view>
					<text>开户名称</text>
					<input type="text" placeholder="请输入开户名称" v-model="account"/>
				</view>
				<view>
					<text>对公银行账户</text>
					<input type="number" placeholder="请输入对公银行账户" v-model="bank"/>
				</view>
			</view>
			
			<!-- 认证联系人信息 -->
			<view class="authent">
				<view class="title">认证联系人信息</view>
				<view>
					<text>法人姓名</text>
					<input type="text" placeholder="请输入法人姓名" v-model="name"/>
				</view>
				<view>
					<text>联系人电话</text>
					<input type="number" placeholder="请输入联系人电话" v-model="telephone"/>
				</view>
				<view>
					<text>法人身份证号码</text>
					<input type="number" placeholder="请输入法人身份证号码" v-model="idcard"/>
				</view>
			</view>
			
			<!-- 距离 -->
			<view class="distance"></view>
			
			<!-- 提交 -->
			<view class="release" :class="{ active: isActive }" @click="flagg && suBmitd()">
			提交
			</view>
		</view>
		<!-- 提示用户上传成功与否 -->
		<view class="warp" v-if="relend">
			<view class="warp-view tipmin">
				<text>{{reldata}}</text>
			</view>
		</view>
		
		<!-- 审核状态 -->
		<stateing ref="mon"></stateing>
		
	</view>
</template>

<script>
	import {uploadimage} from '../../common/list.js'
	import {uploads} from '../../common/uploads.js'
	// 引入审核组件
	import stateing from '../../element/stateing.vue'
	var db = wx.cloud.database()
	var Authentusers = db.collection('Authentication')
	var users = db.collection('Authentication')
	// 上传静态资源的云存储文件
	var resou = 'Authentication'
	export default{
		components:{
			stateing
		},
		data() {
			return {
				relend:false,
				reldata:'正在提交...请勿关闭该页面',
				// 整个页面
				pageauth:false,
				// 判断公用上传参数
				top:'top',
				logo:'logo',
				flagg:false,
				isActive:false,
				enterprise:'',
				topimg:[],
				logoimg:[],
				account:'',
				bank:'',
				name:'',
				telephone:'',
				idcard:''
			}
		},
		methods:{
			// 上传图片
			uploadImg(imgs){
				console.log(imgs)
				let many = 1
				uploadimage(many)
				.then((res)=>{
					console.log(res)
					if(imgs == 'top'){
						this.topimg = res
					}else if(imgs == 'logo'){
						this.logoimg = res
					}
					
				})
				.catch((err)=>{
					console.log(err)
				})
			},
			suBmitd(){
				console.log('提交')
				// console.log(this.enterprise)
				// console.log(this.topimg)
				// console.log(this.account)
				// console.log(this.bank)
				// console.log(this.name)
				// console.log(this.telephone)
				// console.log(this.idcard)
				this.relend = true
				this.userdata()
				
			},
			
			// 上传认证数据
			async userdata(){
				// 等待上传工商执照图片等待返回链接
				let staticimg = await this.staticImg()
				// 等待上传logon
				let logoticimg = await this.logoticImg()
				// 上传所有数据到数据库
				await this.cloudData(staticimg,logoticimg)
			},
			
			// 上传工商执照图片
			staticImg(){
				return new Promise((resolve,reject)=>{
					uploads(this.topimg,resou)
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
			// 上传logo
			logoticImg(){
				return new Promise((resolve,reject)=>{
					uploads(this.logoimg,resou)
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
			cloudData(staticimg,logoticimg){
				let datas = {
					enterprise:this.enterprise,
					topimg:staticimg,
					logoimg:logoticimg,
					account:this.account,
					bank:this.bank,
					name:this.name,
					telephone:this.telephone,
					idcard:this.idcard
				}
				wx.cloud.callFunction({
				  name:'authening',
				  data:{
					userDetail: datas,
					examine:'Being'
				  }
				})
				.then((res)=>{
					console.log(res)
					// 提交成功隐藏提示和整个页面，显示正在审核
					this.relend = false
					this.pageauth = false
					// 弹出模态框
					let staimg = '../static/img/zhengzai.svg'
					let title = '正在审核中'
					this.compstate(staimg,title)
				})
				.catch((err)=>{
					console.log(err)
				})
				
			},
			
			// 审核状态
			statedatas(){
				users.get()
				.then((res)=>{
					console.log(res)
					let examine = res.data[0].examine
					if(examine && examine == 'Being'){
						// 正在审核中
						this.pageauth = false
						let staimg = '../static/img/zhengzai.svg'
						let title = '正在审核中'
						this.compstate(staimg,title)
					}else if(examine && examine == 'success'){
						// 审核成功
						this.pageauth = false
						let staimg = '../static/img/success.svg'
						let title = '认证成功'
						this.compstate(staimg,title)
					}else if(examine && examine == 'fail'){
						// 审核失败
						this.pageauth = false
						let staimg = '../static/img/fail.svg'
						let title = '认证失败'
						this.compstate(staimg,title)
					}else if(!examine){
						this.pageauth = true
					}
				})
				.catch((err)=>{
					console.log(err)
				})
			},
			
			// 被调用的审核组件
			compstate(staimg,title){
				this.$nextTick(()=>{   //dom更新循环结束之后的延迟回调
					this.$refs.mon.init(staimg,title)
				})
			}
		},
		
		// 审核状态
		mounted() {
			this.statedatas()
		},
		
		// 判断
		computed:{
			testdata(){
				if(this.enterprise !='' && this.topimg.length != 0 && this.logoimg.length != 0 && this.account != '' && this.bank != '' && this.name != '' && this.telephone != '' && this.idcard != ''){
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
</style>
