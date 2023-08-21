<template>
	<view class="container1">
		<view class="title1">
			微信教务系统小程序登录
		</view>
		<view class="br"></view>
		<view class="br"></view>
		<form @submit="formSubmit">
			<view>
				<input name="username" placeholder="学号/工号"/>
			</view>
			<view class="br"></view>
			<view>
				<input password name="password" type="safe-password" placeholder="密码"/>
			</view>
			<view class="br"></view>
			
			<radio-group name="radio">
				<radio value='teacher'>教师</radio>
				<radio value='student'>学生</radio>
			</radio-group>
			
			<view class="br"></view>
			<view>
				<button form-type="submit">登录</button>
			</view>
		</form>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			formSubmit: function(e) {
				var username = e.detail.value.username;
				var password = e.detail.value.password;
				
				// 这里做一个单选，选择教师身份 or 学生身份
				var admin = e.detail.value.radio;
				var loginform;
				// 验证表单
				if(username!=""&&password!=""&&admin!="") {
					
					if(admin == "student"){
						loginform = '/loginStudent'
					}else{
						loginform = '/loginTeacher'
					}
					// 验证后发起请求，调用后台方法（先实现学生端）
					uni.request({
						url:'http://localhost:10010/login'+loginform,
						data:{
							username:username,
							password:password
						},
						method:'POST',
						success:(res)=>{
							// 登录成功
							if(res.data.flag) {
								uni.showToast({
									icon:'success',
									title:'登录成功！'
								})
								uni.setStorageSync('token',res.data.msg);	// 设置token
								uni.setStorageSync('useradmin',admin);	// 设置权限
								uni.setStorageSync('userid',username);		// 设置id值
								uni.reLaunch({
									url:'/pages/enter/enter'
								})
							// 登录失败
							}else{
								uni.showToast({
									icon:'error',
									title:'账号或密码错误'
								})
							}
						}
					})
				}else{
					uni.showToast({
						icon:'error',
						title:'请完整填入信息'
					})
				}
			}
		}
	}
</script>

<style>
.title1 {
	font-size: 40rpx;
	text-align: center;
}
input {
	border: 1rpx solid #eee;
	margin: 5rpx;
	padding: 15rpx;
	border-radius: 3rpx;
}
button {
	width: 50%;
	height: 75rpx;
	line-height: 75rpx;
}
.container1 {
	padding: 50rpx;
	background-color: white;
	position: absolute;
	top: 20%;
	left: 10%;
	right: 10%;
}
.br {
	height: 20rpx;
}
</style>
