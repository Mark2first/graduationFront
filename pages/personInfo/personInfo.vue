<template>
	<view>
		<uni-table>
			<uni-tr>
				<uni-th align="center" width="200rpx">学号/工号</uni-th>
				<uni-th align="center">
					{{personInfo.userId}}
				</uni-th>
			</uni-tr>
			<uni-tr>
				<uni-th align="center" width="200rpx">姓名</uni-th>
				<uni-th align="center">
					{{personInfo.userName}}
				</uni-th>
			</uni-tr>
			<uni-tr>
				<uni-th align="center" width="200rpx">所在学院</uni-th>
				<uni-th align="center">{{personInfo.collegeName}}</uni-th>
			</uni-tr>
			<template v-if="judgeStudent()">
				<uni-tr>
					<uni-th align="center" width="200rpx">所在班级</uni-th>
					<uni-th align="center">{{personInfo.classesName}}</uni-th>
				</uni-tr>
			</template>
			<uni-tr>
				<uni-th align="center" width="200rpx">性别</uni-th>
				<uni-th align="center">{{personInfo.sex}}</uni-th>
			</uni-tr>
			<uni-tr>
				<uni-th align="center" width="200rpx">手机号</uni-th>
				<uni-th align="center">
					<span v-if="judgeEdit()">{{personInfo.phone}}</span>
					<input v-else v-model="personInfo.phone"/>
				</uni-th>
			</uni-tr>
			<uni-tr>
				<uni-th align="center" width="200rpx">居住地址</uni-th>
				<uni-th align="center">
					<span v-if="judgeEdit()">{{personInfo.address}}</span>
					<input v-else v-model="personInfo.address"/>
				</uni-th>
			</uni-tr>
			<uni-tr>
				<uni-th align="center" width="200rpx">出生日期</uni-th>
				<uni-th align="center">
					<span v-if="judgeEdit()">{{personInfo.birthday}}</span>
					<input v-else v-model="personInfo.birthday"/>
				</uni-th>
			</uni-tr>
		</uni-table>
	</view>
	<view>
		<!-- 这里预留判断条件 -->
		<template v-if="judgeEdit()">
			<button type="primary" @click="clickEdit()">编辑</button>
			
		</template>
		<template v-else>
			<button type="primary" @click="ApplyEdit()">确认修改</button>
		</template>
	</view>
	
</template>

<script>
import personInfoVue from './personInfo.vue';
	export default {
		data() {
			return {
				edit:false,
				personInfo:{
					// 示例代码
				}
			}
		},
		methods: {
			/**
			 * 判断身份
			 */
			judgeStudent() {
				var useradmin = uni.getStorageSync('useradmin');
				if(useradmin=="student"){
					return true;
				}
				return false;
			},
			/**
			 * 判断
			 */
			judgeEdit() {
				return !this.edit;
			},
			/**
			 * 点击编辑按钮
			 */
			clickEdit() {
				this.edit = true;
			},
			
			/**
			 * 显示数据
			 */
			showAll() {
				uni.showLoading({
					title: '加载中'
				});
				var userid = uni.getStorageSync('userid');
				var token = uni.getStorageSync('token');
				var useradmin = uni.getStorageSync('useradmin');
				var admin;
				var that = this;
				// 判断权限
				if(useradmin == "student") {
					admin = '/manageStudent'
				}else{
					admin = '/teacher'
				}
				uni.request({
					url: 'http://localhost:10010'+admin+'/'+userid,
					method:'GET',
					header: {
						'adminToken':token,
						'userAdmin':useradmin,
						'userId':userid,
					},
					success: (res)=>{
						if(useradmin == "student") {
							that.personInfo.userId = res.data.studentId;
							that.personInfo.userName = res.data.studentName;
							that.personInfo.classesName = res.data.classes.classesName;
							that.personInfo.sex = res.data.studentSex;
							that.personInfo.phone = res.data.studentPhone;
							that.personInfo.address = res.data.studentAddress;
							that.personInfo.birthday = res.data.studentBirthday;
							uni.hideLoading();
						}else{
							that.personInfo.userId = res.data.teacherId;
							that.personInfo.userName = res.data.teacherName;
							that.personInfo.sex = res.data.teacherSex;
							that.personInfo.phone = res.data.teacherPhone;
							that.personInfo.address = res.data.teacherAddress;
							that.personInfo.birthday = res.data.teacherBirthday;
							uni.hideLoading();
						}
						that.personInfo.collegeName = res.data.college.collegeName;
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
			
			// 预留接口
			ApplyEdit() {
				
				var that = this;
				var userid = uni.getStorageSync('userid');
				var token = uni.getStorageSync('token');
				var useradmin = uni.getStorageSync('useradmin');
				var admin;
				var InfoModify;
				// 判断权限
				if(useradmin == "student") {
					admin = '/manageStudent'
					InfoModify = {
						studentId : this.personInfo.userId,
						studentBirthday: this.personInfo.birthday,
						studentAddress: this.personInfo.address,
						studentPhone: this.personInfo.phone,
					}
				}else{
					admin = '/teacher'
					InfoModify = {
						teacherId : this.personInfo.userId,
						teacherBirthday: this.personInfo.birthday,
						teacherAddress: this.personInfo.address,
						teacherPhone: this.personInfo.phone,
					}
				}
				
				uni.showModal({
					title: '提示',
					content: '是否确认修改',
					success: function (res) {
						if (res.confirm) {
							uni.showLoading({
								title: '加载中'
							});
							// 调用方法
							uni.request({
								url:'http://localhost:10010'+admin,
								method:'PUT',
								data:InfoModify,
								header: {
									'adminToken':token,
									'userAdmin':useradmin,
									'userId':userid,
								},
								success: (res)=>{
									uni.hideLoading();
									that.edit = false;
									that.showAll();
									uni.showToast({
										title:'修改成功'
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
							console.log(InfoModify)
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});
			},
		},
		created() {
			this.showAll();
		}
	}
</script>

<style>
input {
  border: 1px solid #eee;
  margin: 5px;
  padding: 5px;
  border-radius: 3px;
}
</style>
