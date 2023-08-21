<template>
	<template v-for="item in listData">
		<view class="frontClass" @click="clickDetail(item)">
			<view style="font-size: 35rpx;font-weight: 700;">{{item.infoTitle}}</view>
			<view style="font-size: 30rpx;">
				<view style="text-align: left;display: block;">{{item.infoReleaseDate}}</view>
				<view style="text-align: right;display: inline-block;">阅读量：{{item.infoReadCount}}</view>
			</view>
		</view>
		<view class="line1"></view>
	</template>
</template>

<script>
	export default {
		data() {
			return {
				listData:[
					// {
					// 	infoId:1,
					// 	infoTitle: '测试1',
					// 	infoReleaseDate: '2022-01-01',
					// 	infoReadCount: '',
					// },
					// {
					// 	infoId:2,
					// 	infoTitle: '测试2',
					// 	infoReleaseDate: '2022-01-02',
					// 	infoReadCount: '',
					// }
				]
			}
		},
		methods: {
			clickDetail(item) {
				// 跳转页面
				uni.navigateTo({
					url:'/pages/InfoDetail/InfoDetail?infoId='+item.infoId
				})
			},
			showAll(){
				// uni.showLoading({
				// 	title:'正在加载中'
				// })
				var userid = uni.getStorageSync('userid');
				var token = uni.getStorageSync('token');
				var useradmin = uni.getStorageSync('useradmin');
				var that = this;
				var adminId;
				if(useradmin == 'student') {
					adminId = Number(2)
				}else{
					adminId = Number(1)
				}
				uni.request({
					url: 'http://localhost:10010/info/infoservice/infoUser/'+adminId,
					method: 'GET',
					header: {
						'adminToken':token,
						'userAdmin':useradmin,
						'userId':userid,
					},
					success(res) {
						that.listData = res.data
						uni.hideLoading()
					},complete: (complete) => {  
						if(complete.statusCode==401) {
							uni.removeStorageSync('token')
							uni.removeStorageSync('userid')
							uni.removeStorageSync('useradmin')
							uni.reLaunch({
								url:'/pages/login/login'
							})
						}
					}
				})
			},
		},
		created() {
			this.showAll();
		}
	}
</script>

<style>
.line1 {
	border-bottom: 1rpx solid gray;
}
.frontClass {
	margin: 15rpx;
	padding: 8rpx;
	font-size: 40rpx;
	background-color: gainsboro;
	border-radius: 20rpx;
}
</style>
