<template>
	<view class="container">
		<view class="list-item list-item-heigher">
			<view class="left">昵称</view>
			<input type="text" placeholder="{{nickname}}" v-model="this.nickname"/>
		    </view>
			<button @click="addCourse(course)" class="btn">修改</button>
	</view>
</template>

<script>
	export default{
		data(){
			return{
				nickname:''
			};
		},
		onLoad() {
			},
			onShow:function() {
					var _this = this;
				const loginKey = uni.getStorageSync('login_key');
				
				if (loginKey) {
					// console.log(loginKey);
					this.storageData = {
						login: loginKey.login,
						nickname: loginKey.nickname,
						avatar: loginKey.avatar
					};
				}else{
					this.storageData ={
						login: false
					};
				}
				uni.request({
					url: 'http://47.100.178.47:8080/api/user/' + uni.getStorageSync('login_key').userId,
					method: 'GET',
					header: { 'content-type': 'application/json' },
					success: res => {
						if (res.data.code === 0) {
							console.log(res.data.data.avatar+'————————————');
							_this.avatar = res.data.data.avatar;
							_this.nickname = res.data.data.nickname;
						}
					}
				});
				
			},
			updateName: function(user) {
				var _this = this;
				user.name = this.nickname;
				this.$http({
					method: 'post',
					url: 'http://47.100.178.47:8080/api/user',
					data: {
						userId: _this.loginUserId,
						courseName: course.courseName,
						courseClass: course.courseClass,
						cover: course.cover,
						finished: 0
					}
				}).then(function() {
					alert('新增班课成功');
					_this.$router.push('/');
				});
			}
				
		
	}
</script>

<style>
</style>
