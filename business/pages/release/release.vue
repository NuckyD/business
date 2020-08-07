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
	// 引入审核组件
	import stateing from '../../element/stateing.vue'
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
