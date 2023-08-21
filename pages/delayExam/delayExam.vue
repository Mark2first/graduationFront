<template>
	<view>
		<!-- 一个页面，然后有一个下拉栏，用于显示本学期开设的课程 -->
		<form @submit="formSubmit">
			<view style="display: flex; flex-direction: row;">
				<!-- 这里课程选项栏的value挂载RelationId -->
				选择课程
				<radio-group name="requestCtRelationId">
					<template v-for="item in optionsCourse">
						<radio :value="item.value">{{item.label}}</radio>
						<view></view>
					</template>
				</radio-group>
				<checkbox-group>
					
				</checkbox-group>
			</view>
			<view class="br"></view>
			<view style="display: flex; flex-direction: row;">
				申请理由
				<textarea style="border:1rpx solid black;" v-model="areaContent"></textarea>
			</view>
			<view class="br"></view>
			<button form-type="submit" type="primary">确认申请</button>
		</form>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				optionsCourse:[
					// {
					// 	label:'数据库原理与应用',
					// 	value: 1
					// },
					// {
					// 	label:'计算机组成原理',
					// 	value: 2
					// }
				],
				areaContent:'',
			}
		},
		methods: {
			// 提交事件
			formSubmit:function(e) {
				uni.showLoading({
					title: '加载中'
				});
				var userid = uni.getStorageSync('userid');
				var token = uni.getStorageSync('token');
				var useradmin = uni.getStorageSync('useradmin');
				var that = this;
				var submitForm = {
					requestStudentId:Number(userid),
					requestCtRelationId:Number(e.detail.value.requestCtRelationId),
					requestReason:this.areaContent,
					requestDate:new Date()
				};
				// 调用接口访问链接
				console.log(submitForm)
				uni.request({
					url: 'http://localhost:10010/info/relay',
					method: 'POST',
					data: submitForm,
					header: {
						'adminToken':token,
						'userAdmin':useradmin,
						'userId':userid,
					},
					success(res) {
						uni.hideLoading();
						if(res){
							uni.showToast({
								title:'提交成功',
							})
							uni.redirectTo({
								url:'/pages/enter/enter'
							})
						}else{
							uni.showToast({
								title:'提交失败',
							})
						}
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
			
			// 更改选项框绑定值
			bindPickerChangeForWeekCount(e) {
				this.indexOptionsCourse = e.detail.value;
			},
			
			// 查询课程信息
			showAll() {
				var userid = uni.getStorageSync('userid');
				var token = uni.getStorageSync('token');
				var useradmin = uni.getStorageSync('useradmin');
				var that = this;
				uni.request({
					url: 'http://localhost:10010/courseTable/student/studentSelectCourse/'+userid,
					method: 'GET',
					header: {
						'adminToken':token,
						'userAdmin':useradmin,
						'userId':userid,
					},
					success(res) {
						that.optionsCourse = res.data;
						console.log(that.optionsCourse);
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
.br {
	height: 20rpx;
}
</style>
