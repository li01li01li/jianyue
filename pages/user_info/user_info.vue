<template>
	<view class="container">
		<view class="list">
			<view class="list-item list-item-heigher">
				<view class="left">昵称</view>
				<view class="text">
				<input class="uni-input" type="text" value="" v-model="renickname" required="required" />
                 </view>
				
			</view>
		</view>
		<view class="list-item list-item-heigher">
			<view class="left">头像</view>
			<view class="right">
				<image :src="avatar" class="avatar" @tap="showActionSheet"></image>
			</view>
		</view>
		<view class="list-item list-item-heigher">
			<view class="left">修改密码</view>
		</view>
	<button class="green-btn" @tap="changeNickname(renickname)" >确认修改</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				nickname: '',
				avatar: '',
				userId: uni.getStorageSync('login_key').userId,
				renickname: ''
			};
		},
		onLoad() {},
		onShow() {
			var _this = this;
			uni.request({
			    url: 'http://localhost:8080/api/user/' + uni.getStorageSync('login_key').userId,
				method: 'GET',
				header: {
					'content-type': 'application/json'
				},
				success: res => {
					if (res.data.code === 0) {
						console.log(res.data.data.avatar + '————————————');
						_this.avatar = res.data.data.avatar;
						_this.nickname = res.data.data.nickname;
					}
				}
			});
		},

		methods: {
			changeNickname: function(renickname) {
				var _this = this;
				uni.request({
					url: 'http://localhost:8080/api/user/nickname?id=' + uni.getStorageSync('login_key').userId,
					method: 'put',
					data: renickname,
					header: {
						'content-type': 'application/json'
					},
					success: res => {

						uni.navigateBack({
							delta: 1,
							animationType: 'pop-out',
							animationDuration: 200
						})
					}
				})
			},
			showActionSheet: function() {
				console.log('show');
				var _this = this;
				uni.showActionSheet({
					itemList: ['拍照', '从相册选择'],
					success: function(res) {
						console.log('选中了第' + (res.tapIndex + 1) + '个按钮');
						//选择的是拍照功能
						if (res.tapIndex == 0) {
							uni.chooseImage({
								count: 1,
								sourceType: ['camera'],
								success: function(res) {
									uni.saveImageToPhotosAlbum({
										filePath: res.tempFilePaths[0],
										success: function() {
											console.log('save success');
											uni.uploadFile({
												url: 'http://localhost:8080/api/user/avatar',
												filePath: res.tempFilePaths[0],
												name: 'file',
												formData: {
													userId: _this.userId
												},
												success: uploadFileRes => {
													console.log(uploadFileRes.data);
													_this.avatar = uploadFileRes.data;
												}
											});
										}
									});
								}
							});
						}
						//从相册选择
						if (res.tapIndex == 1) {
							uni.chooseImage({
								count: 1, //默认9
								sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都
								sourceType: ['album'], //从相册选择
								success: function(res) {
									console.log(JSON.stringify(res.tempFilePaths));
									uni.uploadFile({
										url: 'http://localhost:8080/api/user/avatar', //仅为示例，非真实的接口地址
										filePath: res.tempFilePaths[0],
										name: 'file',
										formData: {
											userId: _this.userId
										},
										success: uploadFileRes => {
											console.log(uploadFileRes.data);
											_this.avatar = uploadFileRes.data;
										}
									});
								}
							});
						}
					},
					fail: function(res) {
						console.log(res.errMsg);
					}
				});
			}

		}
	}
</script>

<style>
	.list-item-heigher {
		height: 80px;
		display: flex;
	}

	.left {
		flex: 1 1 40%;
		margin-left: 10px;
		margin-top: 25px;
	}
    .text{
		margin-top: 25px;
	}
	.right {
		flex: 1 1 60%;
		margin-top: 10px;
	}
	
	.green-btn{
		color: white;
	}
	button{
		background-color: green;
	}
</style>
