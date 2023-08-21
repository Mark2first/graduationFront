<template>
	<view style="height: 70rpx; line-height: 70rpx; display: flex; flex-direction: row; text-align: center;">
		<view style="width: 120rpx;"></view>
		<view>学年：</view>
		<picker @change="bindPickerChangeForYear" :value="indexYear" :range="arrayYear" >
			<view>{{arrayYear[indexYear]}}</view>
		</picker>
		<view style="width: 100rpx;"></view>
		<view>学期：</view>
		<picker @change="bindPickerChangeForSemester" :value="indexSemester" :range="arraySemester" >
			<view>{{arraySemester[indexSemester]}}</view>
		</picker>
		<button @click="searchScore" style="height: 70rpx; line-height: 70rpx;" size="mini" type="primary">确定</button>
	</view>
	<uni-table>
		<uni-tr>
			<uni-th align="center" width="100">课程名称</uni-th>
			<uni-th align="center" width="45">课程成绩</uni-th>
			<uni-th align="center" width="45">课程学分</uni-th>
			<uni-th align="center" width="100">课程学年</uni-th>
			<uni-th align="center" width="45">课程性质</uni-th>
		</uni-tr>
		<uni-tr v-for="item in courseScore">
			<template v-if="item.scoreIsConfirm == 1">
				<uni-td align="center">{{item.course.courseName}}</uni-td>
				<uni-td align="center">{{item.score}}</uni-td>
				<uni-td align="center">{{item.course.courseCredit}}</uni-td>
				<uni-td align="center">{{item.courseTeacherRelation.courseYear}}-{{item.courseTeacherRelation.courseSemester}}</uni-td>
				<uni-td align="center">{{item.course.courseNature}}</uni-td>
			</template>
		</uni-tr>
	</uni-table>
</template>

<script>
	export default {
		data() {
			return {
				arrayYear:['2021-2022','2022-2023','2023-2024'],
				indexYear:0, 
				
				arraySemester:['1','2','3'],
				indexSemester:0,
				courseScore:[],
			}
		},
		methods: {
			bindPickerChangeForYear(e) {
				console.log('picker发送选择改变，携带值为', e.detail.value)
				this.indexYear = e.detail.value
			},
			bindPickerChangeForSemester(e) {
				console.log('picker发送选择改变，携带值为', e.detail.value)
				this.indexSemester = e.detail.value
			},
			searchScore() {
				var userid = uni.getStorageSync('userid');
				var token = uni.getStorageSync('token');
				var useradmin = uni.getStorageSync('useradmin');
				var that = this;
				var ObjectSearchStudentScore = {
					studentId: Number(userid),
					courseYear: this.arrayYear[this.indexYear],
					courseSemester: this.arraySemester[this.indexSemester],
				}
				uni.showLoading({
					title: '加载中'
				});
				uni.request({
					url: 'http://localhost:10010/courseTable/student/score/searchPersonScore',
					method: 'POST',
					data: ObjectSearchStudentScore,
					header: {
						'adminToken':token,
						'userAdmin':useradmin,
						'userId':userid,
					},
					success(res) {
						that.courseScore = res.data;
						console.log(that.courseScore);
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
	}
</script>

<style>

</style>
