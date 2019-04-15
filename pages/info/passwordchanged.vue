 <template>
		<view class="uni-flex uni-column container">
		<input
			class="uni-input"
			type="text"
			value=""
			placeholder="请修改"
			v-model="password"
			required="required"
		/>
		<view class="right">
			<button v-show="show" class="message" @tap="getVerifyCode">获取验证码</button>
			<button v-show="!show" class="message">{{ count }}s后重新获取</button>
		</view>
		<button class="green-btn" @tap="changePassword(password)">确认修改</button>
	</view>
</template>

<script>
export default {
	data() {
		return {
			password:'',
			show: true,
			count: '',
			timer:null,
			
			mobile: '',
			verifyCode: ''
		};
	},
	onLoad() {
	},
	methods: {

			getVerifyCode: function(password) {
			const TIME_COUNT = 20;
		
			var _this = this;
			uni.request({
				url:  this.apiServer+'/user/password?id='+uni.getStorageSync('login_key').userId,
				method: 'put',
				data:password,
				header: { 'content-type': 'application/json' },
				success: res => {
					uni.navigateBack();
				}
			});
			uni.request({
				url: this.apiServer + '/user/verify',
				method: 'POST',
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				data: {
					mobile: _this.mobile
				},
				success: res => {
					if (res.data.code === 0) {
						uni.showToast({
							title: '验证码已发送'
						});
						_this.disabled = true;
						console.log(_this.disabled);
							if (!this.timer) {
							this.count = TIME_COUNT;
							this.show = false;
							this.timer = setInterval(() => {
								if (this.count > 0 && this.count <= TIME_COUNT) {
									this.count--;
								} else {
									this.show = true;
									clearInterval(this.timer);
									this.timer = null;
								}
							}, 1000);
						}
					} else {
						uni.showModal({
							title: '提示',
							content: res.data.msg
						});
					}
				}
			});
		
		},
		checkCode: function() {
			var _this = this;
			console.log(_this.verifyCode);
			uni.request({
				url: this.apiServer + '/user/check',
				method: 'POST',
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				data: {
					mobile: _this.mobile,
					verifyCode: _this.verifyCode
				},
				success: res => {
					console.log(res.data);
					if (res.data.code === 0) {
						uni.navigateTo({
							url: '../signin/password?mobile=' + _this.mobile
						});
					} else {
						uni.showModal({
							title: '提示',
							content: res.data.msg
						});
					}
				}
			});
		}
	}
};
</script>

<style>
input {
	height: 50px;
	border-bottom: 1px solid #eee;
	margin-bottom: 5px;
}
</style>