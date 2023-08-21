<template>
	<swiper class="container1" indicator-dots>
		<swiper-item class="item">
			欢迎使用微信小程序教务系统
		</swiper-item>
	</swiper>
	
	<view class="container12">
		<text :value="userInfo">欢迎
		<template v-if="userInfo.useradmin=='student'">
			<text>学生</text>
		</template>
		<template v-if="userInfo.useradmin=='teacher'">
			<text>教师</text>
		</template>
		用户: {{userInfo.username}}</text>
	</view>
	
	<view class="container2">
		<!-- 课表查询通用 -->
		<view @click="ClickClassTable()">个人课表</view>
		<view @click="ClickClassroom()">教室查询</view>
	</view>
	
	<view class="br"></view>
	<view class="container2" :value="userInfo">
		<view @click="ClickPerson()">个人信息维护</view>
		<!-- 学生/教师分别查看 -->
		<template v-if="userInfo.useradmin=='student'">
			<view @click="ClickDelay()">申请缓考</view>
		</template>
		<!-- <template v-if="userInfo.useradmin=='teacher'">
			<view @click="ClickDelay()">查看学生缓考申请</view>
		</template> -->
	</view>
	
	<view class="br"></view>
	<view class="container2" :value="userInfo">
		<!-- 学生专用 -->
		<template v-if="userInfo.useradmin=='student'">
			<view @click="ClickScore()">成绩查询</view>
			<!-- <view @click="ClickMajor()">专业教学大纲查询</view> -->
		</template>
		
		<!-- 教师专用 -->
		<!-- <template v-if="userInfo.useradmin=='teacher'">
			<view @click="">发布通知</view>
			<view @click="">更多功能（待定）</view>
		</template> -->
		
	</view>
	
	<view class="br"></view>
	<view class="br"></view>
	<view class="br"></view>
	<view style="text-align: center;">
	  后面是一片未知的探索区域
	</view>
	
</template>

<script>
	export default {
		data(){
			return{
				userInfo:{
					username:uni.getStorageSync('userid'),
					useradmin:uni.getStorageSync('useradmin')
				}
			}	
		},
		methods: {
			/**
			 * token检查
			 */
			LoadTokendetack() {
				var token = uni.getStorageSync('token')
				console.log(token);
				if(!token) {
					uni.removeStorageSync('useradmin')
					uni.removeStorageSync('userid')
					uni.redirectTo({
						url:'/pages/login/login'
					})
				}
			},
			
			/**
			 * 跳转课表界面
			 */
			ClickClassTable() {
				uni.navigateTo({
					url:'/pages/testWebView/testWebView'
				})
			},
			/**
			 * 跳转成绩查询界面
			 */
			ClickScore() {
				uni.navigateTo({
					url:'/pages/score/score'
				})
			},
			/**
			 * 个人信息维护界面
			 */
			ClickPerson() {
				uni.navigateTo({
					url:'/pages/personInfo/personInfo'
				})
			},
			/**
			 * 教室查询页面
			 */
			ClickClassroom() {
				uni.navigateTo({
					url:'/pages/classroomSearch/classroomSearch'
				})
			},
			/**
			 * 延迟考试界面
			 */
			ClickDelay() {
				uni.navigateTo({
					url:'/pages/delayExam/delayExam'
				})
			},
			/**
			 * 专业大纲页面查询
			 */
			ClickMajor() {
				uni.navigateTo({
					url:'/pages/majorSearch/majorSearch'
				})
			}
		},
		created() {
			this.LoadTokendetack();
		}
	}
</script>

<style>
	.swiper-container {
		height: 350rpx;
	}
	
	.container12 {
		height: 50rpx;
		line-height: 50rpx;
		text-align: center;
		background-color: white;
		border-radius: 10rpx;
		margin: 30rpx;
	}
	
	.container1 {
		height: 350rpx;
	}
	
	.item {
		height: 100%;
		line-height: 350rpx;
		text-align: center;
	}
	
	swiper-item:nth-child(1).item {
		background-color: lightskyblue;
	}
	
	.container2 {
		display: flex;
		justify-content: space-around;
	}
	
	.container2 view {
		width: 340rpx;
		height: 150rpx;
		text-align: center;
		line-height: 150rpx;
		background-color: aliceblue;
	}
	
	.br {
	  height: 20rpx;
	}


</style>

