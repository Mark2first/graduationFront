<template>
	<view style="font-size: 50rpx;text-align: center;">
		{{ArticleDetail.infoTitle}}
	</view>
	<view style="font-size: 25rpx;text-align: center;">发布日期：{{ArticleDetail.infoReleaseDate}}</view>
	<view class="line1"></view>
	<view class="frontClass">
		{{ArticleDetail.infoContent}}
	</view>
</template>

<script>
	export default {
		data() {
			return {
				ArticleDetail:{}
			}
		},
		methods: {
			
		},
		onLoad:function(option){
			var id = option.infoId
			var userid = uni.getStorageSync('userid');
			var token = uni.getStorageSync('token');
			var useradmin = uni.getStorageSync('useradmin');
			var that = this;
			uni.request({
				url: 'http://localhost:10010/info/infoservice/infoUser/detail/'+id,
				method: 'GET',
				header: {
					'adminToken':token,
					'userAdmin':useradmin,
					'userId':userid,
				},
				success(res) {
					that.ArticleDetail = res.data
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
	background-color: ghostwhite;
	border-radius: 20rpx;
}
</style>
