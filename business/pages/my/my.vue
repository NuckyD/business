<template>
	<view>
		<login-list v-if="!wxlogin"></login-list>
		<users v-if="wxlogin" :username="username" :nothave="nothave"></users>
	</view>
</template>

<script>
	import Users from './components/users.vue'
	import {mapState} from 'vuex'
	var db = wx.cloud.database()
	var users = db.collection('Authentication')
	export default{
		components:{
			Users
		},
		data() {
			return {
				wxlogin:true,
				sussdata:'',
				username:{},
				nothave:"未认证",
			}
		},
		
		methods:{
			// 用户是否登录
			ifUser(){
				users.get()
				.then((res)=>{
					// length == 0说明没有用户信息，没有登录，发起登录
					if(res.data.length == 0){
						this.wxlogin = false
						console.log('没有登录')
					}else{
						this.wxlogin = true
						console.log('已经登录')
						console.log(res)
						var username = res.data[0]
						console.log(username)
						if(username.examine && username.examine == 'success'){
							console.log('已认证')
							let Detail = username.userDetail
							let Detailobject = {
								nickName:Detail.enterprise,
								avatarUrl:Detail.logoimg
							}
							this.username = Detailobject
							this.nothave = '已认证'
						}else{
							console.log('未认证')
							this.username = username
							this.nothave = '未认证'
						}
					}
				})
				.catch((err)=>{
					console.log(err)
				})
			}
		},
		
		onShow() {
			this.ifUser()
		},
		
		computed:{
			...mapState(['logindata']),
			cont(){
				this.sussdata = this.logindata.login
			}
		},
		watch:{
			sussdata(newValue, oldValue){
				console.log(newValue)
				if(newValue == 'success'){
					this.ifUser()
				}
			}
		}
	}
</script>

<style> 
</style>
