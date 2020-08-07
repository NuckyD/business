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
	export default{
		components:{
		},
		data() {
			return {
				relend:false,
				reldata:'正在提交...请勿关闭该页面',
				// 整个页面
				pageauth:true,
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
			//上传Logo和营业执照图片
			uploadImg(attribute){
				let quantity = 1
				uploadimage(quantity)
				.then((res) => {
					if(attribute === 'logo'){
						this.logoimg = res
					}else if(attribute === 'top'){
						this.topimg = res
					}
				})
			}
		}
	}
</script>

<style scoped>
	@import "../../common/uni.css";
</style>
