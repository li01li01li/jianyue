<template>
	<view class="container">
		<view class="top">
			<text class="article-title">{{article.title}}</text>
		</view>		
		<view class="title-time">
			<view class="pig">
				<view class="pigleft">
				<image :src="article.avatar" class="avatar small"></image>
				</view>
				<view class="textright">
					<text>{{article.nickname}}</text>
			    <text class="time-text">{{ (article.createTime) }}</text>
				</view>
			</view>
			
			
			<view class="kong"></view>
			<view >
				<button v-if="userId != article.uId && !followed" class="butt" @tap="follow">+关注</button>
			</view>
			<view >
				<button v-if="userId != article.uId && followed" class="butt1" @tap="cancelFollow">取消</button>
			</view>
			
		</view>
			
	
		<view class="grace-text"><rich-text :nodes="article.content" bindtap="tap"></rich-text></view>
		<!-- <view>
		<image :src="article.imgs[article.imgs.length - 1].imgUrl"></image>
		</view> -->
		<text class="info-text">评论{{ comments.length}}</text>
		<view class="comment-item" v-for="(comment,index) in comments" :key="index">
			<view class="left">
				<image :src="comment.avatar" class="avatar small"></image>
			</view>
			<view class="right">
				<text>{{comment.nickname}}</text><br />
				<text>{{comment.content}}</text>
				<view>
					<text style="margin-right: 10px;">{{comments.length-index}}楼</text>
					<text>{{ handleTime(comment.commentTime) }}</text>
				</view>
			</view>
		</view>
		<input class="uni input comment-box" type="text" placeholder="写下你的评论" v-model="content" required="required" />
		<button type="primary" @tap="send">提交</button>
	</view>
</template>


<script>
export default {
	data() {
		return {
			article: {
				aId: 0,
				uId: 0,
				title: '',
				content: '',
				avatar: '',
				nickname: '',
				createTime: '',
				imgs:[],
			},
		
			comments: [],
			content: '',
			userId: uni.getStorageSync('login_key').userId,
			followed: false
		};
	},
	onLoad: function(option) {
		//option为object类型，会序列化上个页面传递的参数
		this.article.aId = option.aId;
	},
	onShow: function() {
		this.getArticle();
	},
	onPullDownRefresh: function() {
		this.getArticle();
	},
	methods: {
		getArticle: function() {
			var _this = this;
			uni.request({
				url: this.apiServer + '/article/' + this.article.aId,
				method: 'GET',
				header: { 'content-type': 'application/x-www-form-urlencoded' },
				data: {
					userId: this.userId
				},
				success: res => {
					// console.log(res.data.data.article);
					_this.article.aId = res.data.data.article.id;
					_this.article.uId = res.data.data.article.uid;
					_this.article.title = res.data.data.article.title;
					_this.article.content = res.data.data.article.content;
					_this.article.nickname = res.data.data.article.nickname;
					_this.article.avatar = res.data.data.article.avatar;
					_this.article.createTime = res.data.data.article.createTime;
					_this.comments = res.data.data.comments;
					if (res.data.data.followed === '已关注') {
						_this.followed = true;
					}
				},
				complete: function() {
					uni.stopPullDownRefresh();
				}
			});
		},
		handleTime: function(date) {
			var d = new Date(date);
			var year = d.getFullYear();
			var month = d.getMonth() + 1;
			var day = d.getDate() < 10 ? '0' + d.getDate() : '' + d.getDate();
			var hour = d.getHours() < 10 ? '0' + d.getHours() : '' + d.getHours();
			var minutes = d.getMinutes() < 10 ? '0' + d.getMinutes() : '' + d.getMinutes();
			var seconds = d.getSeconds() < 10 ? '0' + d.getSeconds() : '' + d.getSeconds();
			return year + '-' + month + '-' + day + ' ' + hour + ':' + minutes + ':' + seconds;
		},
		send: function() {
			console.log('评论人编号：' + this.userId + ',文章编号：' + this.article.aId + '，评论内容：' + this.content);
			uni.request({
				url: this.apiServer + '/comment/add',
				method: 'POST',
				header: { 'content-type': 'application/x-www-form-urlencoded' },
				data: {
					aId: this.article.aId,
					uId: this.userId,
					content: this.content
				},
				success: res => {
					if (res.data.code === 0) {
						uni.showToast({
							title: '评论成功'
						});
						this.getArticle();
						this.content = '';
					}
				}
			});
		},
		follow: function() {
			uni.request({
				url: this.apiServer + '/follow/add',
				method: 'POST',
				header: { 'content-type': 'application/x-www-form-urlencoded' },
				data: {
					fromUId: this.userId,
					toUId: this.article.uId
				},
				success: res => {
					if (res.data.code === 0) {
						uni.showToast({
							title: '关注成功'
						});
						this.followed = true;
					}
				}
			});
		},
		cancelFollow: function() {
			uni.request({
				url: this.apiServer + '/follow/cancel',
				method: 'POST',
				header: { 'content-type': 'application/x-www-form-urlencoded' },
				data: {
					fromUId: this.userId,
					toUId: this.article.uId
				},
				success: res => {
					if (res.data.code === 0) {
						uni.showToast({
							title: '已取消关注'
						});
						this.followed = false;
					}
				}
			});
		}
	}
};

</script>

<style scoped>
	.top{
		text-align: center;
	}
	.pig{
		display: flex;
		justify-content: space-between;
		height: 80px;
	}
	.article-title {
		margin-left: 15px;
		font-weight: bold;
		font-size: 22px;
	}

	.title-time {
		display: flex;
		
	}

	.title-time image {
		width: 60px;
		height: 60px;
		border-radius: 50%;
	}

	.read-text {
		color: rgb(110, 110, 110);

	}

	.time-text {
		color: rgb(110, 110, 110);
		float: right;
		/* padding-top: 35upx; */
	}

	.comment-item {
		display: flex;
	}

	.right {
		font-size: 16px;
	}

	.grace-text {
		margin-bottom: 10px;
	}

	.butt {
		background-color: #00C777;
		color: white;
		height: 80upx;
		width: 170upx;
		display: flex;
		text-align: center;
		justify-content: center;
		

	}

	.butt1 {
		background-color: rgb(230, 230, 230);
		color: white;
		height: 80upx;
		width: 170upx;
		display: flex;
		text-align: center;
		justify-content: center;

	}

	.kong {
		width: 160px;
		text-align: center;
	}
</style>
