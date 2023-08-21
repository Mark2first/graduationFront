<template>
	<view>
		<h2 style="text-align: center;">借用具体信息</h2>
		<uni-table>
			<uni-tr>
				<uni-th align="center" width="300rpx">教室名称</uni-th>
				<uni-th align="center">
					{{tableData.classroomName}}
				</uni-th>
			</uni-tr>
			<uni-tr>
				<uni-th align="center" width="300rpx">教室借用学期</uni-th>
				<uni-th align="center">
					{{tableData.courseYear}}-{{tableData.courseSemester}}
				</uni-th>
			</uni-tr>
			<uni-tr>
				<uni-th align="center" width="300rpx">教室借用教学周</uni-th>
				<uni-th align="center">
					第 {{tableData.courseWeekCount}} 周
				</uni-th>
			</uni-tr>
			<uni-tr>
				<uni-th align="center" width="300rpx">教室借用星期</uni-th>
				<uni-th align="center">
					{{tableData.courseWeeks}}
				</uni-th>
			</uni-tr>
			<uni-tr>
				<uni-th align="center" width="300rpx">教室借用具体节数</uni-th>
				<uni-th align="center">
					{{tableData.courseStartTimes}}-{{tableData.courseEndTimes}}
				</uni-th>
			</uni-tr>
			<uni-tr>
				<uni-th align="center" width="300rpx">借用理由</uni-th>
				<uni-th align="center">
					<input style="border: 1rpx solid gray;" v-model="this.tableData.bookingReason"/>
				</uni-th>
			</uni-tr>
		</uni-table>
		<button @click="submitBooking">提交</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				tableData:{},
			}
		},
		methods: {
			// 提交申请
			submitBooking() {
				var userid = uni.getStorageSync('userid');
				var token = uni.getStorageSync('token');
				var useradmin = uni.getStorageSync('useradmin');
				var that = this;
				
				this.tableData.bookingRequestDate = new Date();
				this.tableData.bookingUserId = Number(userid);
				this.tableData.bookingAdmin = useradmin;
				// console.log(this.tableData);
				uni.showLoading({
					title: '正在申请'
				});
				
				uni.request({
					url: 'http://localhost:10010/info/classroom',
					method: 'POST',
					data: that.tableData,
					header: {
						'adminToken':token,
						'userAdmin':useradmin,
						'userId':userid,
					},
					success(res) {
						// that.emptyClassroom = res.data
						uni.hideLoading()
						uni.redirectTo({
							url:'/pages/enter/enter'
						})
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
		},
		// 加载上个页面参数
		onLoad: function(option) {
			this.tableData = JSON.parse(decodeURIComponent(option.item));
			// console.log(this.tableData);
		}
	}
</script>

<style>

</style>
