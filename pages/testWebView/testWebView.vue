<template>
	<view style="text-align: center; display: flex; flex-direction: row;" :value="weekCount">
		<button size="mini" type="primary" :disabled="buttonIsDisplayFront()" @click="buttonSub()">上一周</button>
		<view>第{{weekCount}}周</view>
		<button size="mini" type="primary" :disabled="buttonIsDisplayRear()" @click="buttonAdd()">下一周</button>
	</view>
	<view class="tableViewTr">
		<view class="tableViewTh">
			<view>课时</view>
			<view v-for="(item,index) in courseTimes">
				{{item}}
			</view>
		</view>
		<view class="tableViewTh">
			<view>周一</view>
			<template v-for="(item,index) in monday">
				<view :style="{
					height: judgeCourse(item) + 'rpx',
					lineHeight: '35rpx', 
					fontSize: '20rpx',
					backgroundColor: judgeColor(item),
					}" >
					<text>{{item.name}}</text>
					<text v-if="(item.courseAddress)!=null">
						@{{item.courseAddress}}
					</text>
				</view>
			</template>
		</view>
		<view class="tableViewTh">
			<view>周二</view>
			<template v-for="(item,index) in tuesday">
				<view :style="{
					height: judgeCourse(item) + 'rpx',
					lineHeight: '35rpx', 
					fontSize: '20rpx',
					backgroundColor: judgeColor(item),
					}" >
					<text>{{item.name}}</text>
					<text v-if="(item.courseAddress)!=null">
						@{{item.courseAddress}}
					</text>
				</view>
			</template>
		</view>
		<view class="tableViewTh">
			<view>周三</view>
			<template v-for="(item,index) in wednesday">
				<view :style="{
					height: judgeCourse(item) + 'rpx',
					lineHeight: '35rpx', 
					fontSize: '20rpx',
					backgroundColor: judgeColor(item),
					}" >
					<text>{{item.name}}</text>
					<text v-if="(item.courseAddress)!=null">
						@{{item.courseAddress}}
					</text>
				</view>
			</template>
		</view>
		<view class="tableViewTh">
			<view>周四</view>
			<template v-for="(item,index) in thursday">
				<view :style="{
					height: judgeCourse(item) + 'rpx',
					lineHeight: '35rpx', 
					fontSize: '20rpx',
					backgroundColor: judgeColor(item),
					}" >
					<text>{{item.name}}</text>
					<text v-if="(item.courseAddress)!=null">
						@{{item.courseAddress}}
					</text>
				</view>
			</template>
		</view>
		<view class="tableViewTh">
			<view>周五</view>
			<template v-for="(item,index) in friday">
				<view :style="{
					height: judgeCourse(item) + 'rpx',
					lineHeight: '35rpx', 
					fontSize: '20rpx',
					backgroundColor: judgeColor(item),
					}" >
					<text>{{item.name}}</text>
					<text v-if="(item.courseAddress)!=null">
						@{{item.courseAddress}}
					</text>
				</view>
			</template>
		</view>
		<view class="tableViewTh">
			<view>周六</view>
			<template v-for="(item,index) in saturday">
				<view :style="{
					height: judgeCourse(item) + 'rpx',
					lineHeight: '35rpx', 
					fontSize: '20rpx',
					backgroundColor: judgeColor(item),
					}" >
					<text>{{item.name}}</text>
					<text v-if="(item.courseAddress)!=null">
						@{{item.courseAddress}}
					</text>
				</view>
			</template>
		</view>
		<view class="tableViewTh">
			<view>周日</view>
			<template v-for="(item,index) in sunday">
				<view :style="{
					height: judgeCourse(item) + 'rpx',
					lineHeight: '35rpx', 
					fontSize: '20rpx',
					backgroundColor: judgeColor(item),
					}" >
					<text>{{item.name}}</text>
					<text v-if="(item.courseAddress)!=null">
						@{{item.courseAddress}}
					</text>
				</view>
			</template>
		</view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				weekCount:'01',
				courseTimes:[1,2,3,4,5,6,7,8,9,10,11,12,13],
				monday:[],
				tuesday:[],
				wednesday:[],
				thursday:[],
				friday:[],
				saturday:[],
				sunday:[],
			}
		},
		methods: {
			// 判断一下3-5，10-12节
			threeToFive(item) {
				if(item==13){
					return '';
				}
				if(item==3 || item ==10){
					return '-'+(item+2).toString();
				}
				else{
					return '-'+(item+1).toString();
				}
			},
			
			/**
			 * 计算单课程占据rpx
			 * @param {Object} item
			 */
			judgeCourse(item) {
				return (item.courseEndTimes-item.courseStartTimes+1)*100;
			},
			
			/**
			 * 计算颜色赋予
			 * @param {Object} item
			 */
			judgeColor(item) {
				if(item.name==null){
					return null;
				}else{
					return 'aqua'
				}
			},
			
			/**
			 * button组件群操作
			 */
			
			buttonAdd() {
				var a = parseInt(this.weekCount);
				a = a+1;
				if(a<10){
					this.weekCount = '0'+a.toString();
					this.showAll();
				}else{
					this.weekCount = a.toString();
					this.showAll();
				} 
				
			},
			
			buttonSub() {
				var b = parseInt(this.weekCount);
				b=b-1;
				if(b<10){
					this.weekCount = '0'+b.toString();
					this.showAll();
				}else{
					this.weekCount = b.toString();
					this.showAll();
				} 
			},
			
			buttonIsDisplayFront() {
				if(this.weekCount=="01") return true;
			},
			
			buttonIsDisplayRear() {
				if(this.weekCount=="25") return true;
			},
			
			showAll() {
				uni.showLoading({
					title: '加载中'
				});
				var userid = uni.getStorageSync('userid');
				var token = uni.getStorageSync('token');
				var useradmin = uni.getStorageSync('useradmin');
				var admin;
				// 判断权限
				if(useradmin == "student") {
					admin = '/weChatStudentSearch'
				}else{
					admin = '/weChatTeacherSearch'
				}
				uni.request({
					url:'http://localhost:10010/courseTable/table'+admin+'/'+userid+'/'+this.weekCount,
					method:'GET',
					header: {
						'adminToken':token,
						'userAdmin':useradmin,
						'userId':userid,
					},
					success: (res)=>{
						this.monday = res.data.monday
						this.tuesday = res.data.tuesday
						this.wednesday = res.data.wednesday
						this.thursday = res.data.thursday
						this.friday = res.data.friday
						this.saturday = res.data.saturday
						this.sunday = res.data.sunday
						uni.hideLoading();
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
		created() {
			this.showAll();
		}
	}
</script>

<style>
	.tableViewTr {
		display: flex;
		flex-direction: row;
	}
	.tableViewTh {
		width: 12.5%;
		text-align: center;
		height: 100rpx;
		line-height: 100rpx;
	}
	.br {
		height: 20px;
	}
	.br1 {
		height: 10rpx;
	}
</style>
