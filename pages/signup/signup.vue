<template>
	<view class="container">
		<view class="sign-box">
			<input class="uni-input left" type="number" placeholder="输入手机号" v-model="mobile" required="required" />
			<view class="right">
				<span v-show="show" @tap="getVerifyCode()" class="text">获取验证码</span>
				<span v-show="!show" @tap="getVerifyCode()" class="count">{{count}}s后重新获取</span>
			</view>
		</view>
		<input class="uni-input" type="" placeholder="输入验证码" v-model="verifyCode" required="required" />
		<button @tap="checkCode" class="green-btn">下一步</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				mobile: '',
				verifyCode: '',
				show: true,
				timer: null,
				count: '',


			};
		},
		onLoad() {},
		methods: {

			getVerifyCode: function() {
				const TIME_COUNT = 60;
				var _this = this;
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

						} else {
							uni.showModal({
								title: '提示',
								content: res.data.msg
							});
						}
					}
				});

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

					}, 1000)
				}

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
								url: '../password/password?mobile=' + _this.mobile
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
	.sign-box {
		display: flex;
		align-items: center;
	}

	.left {
		flex: 1 1 60%;
	}

	.right {
		width: 150px;
		height: 38px;
		background-color: rgb(26, 173, 25);
		color: #FFFFFF;
		border-radius: 30px;
		line-height: 40px;
		flex: 1 1 40%;
		text-align: center;
	}

	.uni-input {
		border-radius: 10px;
		margin: 5px 10px 5px 10px;
		border: 1px solid #EEEEEE;

	}

	.green-btn {
		width: 30%;
		background-color: rgb(26, 173, 25);
		color: #FFFFFF;
	}
</style>
