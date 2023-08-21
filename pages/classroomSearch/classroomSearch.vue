<template>
	<view>
		<!-- 教室界面 -->
		<!-- 1 有两个功能，主界面是空教室查询，分时段查询 -->
		<!-- 1.1 查询教室选项：选择学年、学期、教学周、所属校区（必选） -->
		<form @submit="formSubmit">
			<view style="display: flex; flex-direction: row;">
				<view style="width: 200rpx;">
					<view>所属校区：</view>
				</view>
				<radio-group name="campusId">
					<template v-for="item in campus">
						<radio :value="item.value">{{item.label}}</radio>
					</template>
				</radio-group>
			</view>
			<view style="display: flex; flex-direction: row;">
				<view style="width: 150rpx;">
					<view>时间：</view>
				</view>
				<radio-group name="dateDetail" @change="checkboxChange">
					<template v-for="item in dateTimes">
						<radio :value="item.value" :checked="index === current">{{item.label}}</radio>
					</template>
				</radio-group>
			</view>
			<view style="display: flex; flex-direction: row;">
				<view>
					<view>学年：</view>
				</view>
				<picker name="courseYear" @change="bindPickerChangeForYear" :value="indexYear" :range="arrayYear" >
					<view>{{arrayYear[indexYear]}}</view>
				</picker>
				
				<view style="width: 35rpx;"></view>
				<view>
					<view>学期：</view>
				</view>
				<picker name="semester" @change="bindPickerChangeForSemester" :value="indexSemester" :range="arraySemester" >
					<view>{{arraySemester[indexSemester]}}</view>
				</picker>
				
				<view style="width: 35rpx;"></view>
				<view>
					<view>教学周：</view>
				</view>第
				<picker name="weekCount" @change="bindPickerChangeForWeekCount" :value="indexWeekCount" :range="arrayWeekCount" >
					<view>{{arrayWeekCount[indexWeekCount]}}</view>
				</picker>周
				
				<view style="width: 35rpx;"></view>
				<view>
					<view>星期</view>
				</view>
				<picker name="weeks" @change="bindPickerChangeForWeeks" :value="indexWeeks" :range="arrayWeeks" >
					<view>{{arrayWeeks[indexWeeks]}}</view>
				</picker>
			</view>
			<button form-type="submit" type="primary" >查询</button>
		</form>
		<!-- 1.2 然后可选择空置节数进行查询 -->
		<!-- 2 然后是申请预约教室按钮，可指定教室预约 -->
	</view>
	<view class="br"></view>

	<view>
		<h3>空教室查询结果:</h3>
		<template v-for="item in emptyClassroom">
			<view>
				<view class="frontClass" @click="clickBooking(item)">{{item.classroomName}} 点击借用</view>
			</view>
		</template>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				arrayCampus:['2021-2022','2022-2023','2023-2024'],
				indexCampus:0,
				campus:[
					{
						value: 10001,
						label: "清河小营校区",
					},
					{
						value: 10002,
						label: "健翔桥校区",
					},
					{
						value: 10003,
						label: "清河校区",
					},
				],
				dateTimes:[
					{
						value: 'morning',
						label: '上午'
					},
					{
						value: 'afternoon',
						label: '下午'
					},
					{
						value: 'night',
						label: '晚上'
					},
					{
						value: 'allDay',
						label: '全天',
						checked: 'true'
					},
				],
				arrayWeekCount:[
					'1','2','3','4','5',
					'6','7','8','9','10',
					'11','12','13','14','15',
					'16','17','18','19','20'
					],
				indexWeekCount: 0,
				arrayYear:['2021-2022','2022-2023','2023-2024'],
				indexYear:0, 
				
				arraySemester:['1','2','3'],
				indexSemester:0,
				
				arrayWeeks:['monday','tuesday','wednesday','thursday','friday','saturday','sunday'],
				indexWeeks:0,
				
				emptyClassroom:[
					// {
					// 	classroomId:1,
					// 	classroomName:'3-1-201'
					// },
					// {
					// 	classroomId:2,
					// 	classroomName:'3-1-202'
					// }
				],
				dateDetail1:'allDay',
			}
		},
		methods: {
			formSubmit: function(e) {
				// var formData = JSON.stringify(e.detail.value);
				var seUrl = 'http://localhost:10010/classroom/search/emptyroom/'
				+e.detail.value.campusId+'/'
				+this.arrayYear[this.indexYear]+'/'
				+this.arraySemester[this.indexSemester]+'/'
				+this.arrayWeekCount[this.indexWeekCount]+'/'
				+this.arrayWeeks[this.indexWeeks]+'/'
				+e.detail.value.dateDetail;
				var userid = uni.getStorageSync('userid');
				var token = uni.getStorageSync('token');
				var useradmin = uni.getStorageSync('useradmin');
				var that = this;
				console.log(seUrl);
				// uni.showLoading({
				// 	title: '加载中'
				// });
				uni.request({
					url: seUrl,
					method: 'GET',
					header: {
						'adminToken':token,
						'userAdmin':useradmin,
						'userId':userid,
					},
					success(res) {
						that.emptyClassroom = res.data
						// uni.hideLoading()
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
			bindPickerChangeForWeekCount(e) {
				this.indexWeekCount = e.detail.value;
			},
			bindPickerChangeForYear(e) {
				this.indexYear = e.detail.value
			},
			bindPickerChangeForSemester(e) {
				this.indexSemester = e.detail.value
			},
			bindPickerChangeForWeeks(e) {
				this.indexWeeks = e.detail.value;
			},
			checkboxChange(e) {
				this.dateDetail1 = e.detail.value;
				console.log(e.detail.value);
			},
			clickBooking(item) {
				// 看选择的DateDetail
				var start;	// 开始时间
				var end;	// 结束时间
				console.log('dateDetail1 = '+this.dateDetail1)
				if(this.dateDetail1 == 'morning'){
					start = 1,end = 5
				}
				if(this.dateDetail1 == 'afternoon'){
					start = 6,end = 9
				}
				if(this.dateDetail1 == 'night'){
					start = 10,end = 13
				}
				if(this.dateDetail1 == 'allDay'){
					start = 1,end = 13
				}
				// 要传递到下个页面的参数
				var items = {
					// 教室基本信息
					classroomId:item.classroomId,	// 教室ID
					classroomName:item.classroomName,	// 教室名称
					courseWeeks:this.arrayWeeks[this.indexWeeks],	// 具体日期
					courseWeekCount:this.arrayWeekCount[this.indexWeekCount],	// 具体教学周
					courseYear:this.arrayYear[this.indexYear],	// 选择学年
					courseSemester:this.arraySemester[this.indexSemester],	// 选择学期
					courseStartTimes:start,
					courseEndTimes:end,
				}
				// 携带参数跳转页面
				uni.navigateTo({
					url: '/pages/classroomBooking/classroomBooking?item='+encodeURIComponent(JSON.stringify(items))
				});
				// console.log(item)
			},
			
		},
		created() {
			
		}
	}
</script>

<style>
.br {
	height: 20rpx;
}
.frontClass {
	margin: 15rpx;
	padding: 8rpx;
	font-size: 40rpx;
	text-align: center;
	background-color: skyblue;
	border-radius: 20rpx;
}

</style>
