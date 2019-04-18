<template>
	<view class="container">
		<!-- 头像 -->
		<view class="top">
			<view class="avatar-box">
				<view class="avatar-box-s"><image src="../../static/default.png" mode="scaleToFill" class="avatar" v-if="!storageData.login"></image></view>

				<view class="avatar-box-s"><image :src="avatar" mode="scaleToFill" class="avatar" v-if="storageData.login"></image></view>
			</view>
			<view class="info-box">
				<view class="info-box-s"><navigator url="../signin/signin" v-if="!storageData.login"><text>点击登录</text></navigator></view>
			</view>
		</view>
		<!-- 昵称 -->
		<view class="top-name">
			<text v-if="storageData.login">{{ nickname }}</text>

			<navigator url="../setting/setting" v-if="storageData.login" class="btn"><text>个人设置</text></navigator>
		</view>
		
       <!-- <scroll-view class="c1" scroll-x="true">
			
			<view class="c1-1"><image src="../../static/11.png" style="width: 100%;"></image></view>
			<view class="c1-1"><image src="../../static/111.png" mode=""></image></view>
			<view class="c1-1"><image src="../../static/2.jpg" mode=""></image></view>
			
			
		</scroll-view>
    -->

        <!-- 个人-->
		<!-- <view class="middle" "> -->
		<view class="middle" v-if="storageData.login">
			<scroll-view class="grace-tab-title" scroll-x="true" id="grace-tab-title">
				<view v-for="(cate, index) in categories" :key="index" 
				    :data-cateid="cate.cateid" :data-index="index" 
					:class="[cateCurrentIndex == index ? 'grace-tab-current' : '']"
					@tap="tabChange">
					{{ cate.name }}
				</view>
			</scroll-view>
			
			<view class="demo-content">
				<!-- 文章部分 -->
				<view class="content" v-if="cateCurrentIndex === 0">
					
					<view class="list">
						<view class="list-item" v-for="(article, index) in articles" :key="index">
							<view class="list-title">
								<text @tap="gotoDetail(article.id)">{{ article.title }}</text>
							</view>
							<view class="list-time">
								{{ article.createTime }}
							</view>
						
						</view>
					</view>
				</view>
				<!-- 关注部分 -->
				<view class="content" v-if="cateCurrentIndex === 1">
					<view class="list">
						<view class="list-item" v-for="(follow, index) in follows" :key="index">
							<image :src="follow.avatar" class="avatar small"></image>
							<text style="margin-left: 20px;">{{ follow.nickname }}</text>
						</view>
					</view>
				</view>
				<!-- 收藏部分 -->
				<view class="content" v-if="cateCurrentIndex === 2"><text>收藏</text></view>
				<!-- 积分部分 -->
				<view class="content" v-if="cateCurrentIndex === 3">
				
					<view>
						<graceHeader title="弹出层" desc="请点击按钮测试 ^_^ "></graceHeader>
						<view class="grace-bg-white grace-common-mt grace-padding grace-common-border">
							<view style="padding:200rpx 15%;">
								<button type="primary" v-show="dis" @tap="showBanner">点击签到</button>
								<button type="primary" v-show="!dis">签到完成</button>
								<!-- <button type="primary" @tap="showBanner2" style="margin:15px 0;">打开弹出层演示2</button> -->
							</view>
						</view>
						<!-- 弹出层演示 1 -->
						<graceMaskView :show="show" bgcolor="#00C777" v-on:close="closeBanner">
							<view>
								<image src="../../static/qian.jpg" style="width:80%; height: 80px; margin-top:25px; border-top-right-radius:5px; border-top-left-radius:5px;" mode="widthFix"></image>
							</view>
							<view style="padding:25px; padding-bottom:30px;">
								<button type='warn' style="background:#F6644D; padding:0 20px;" @tap="closeBanner">确定</button>
							</view>
						</graceMaskView>
						<!-- 弹出层演示 2 -->
						<!-- <graceMaskView :show="show2" bgcolor="none" v-on:close="closeBanner2">
							<view @tap="tap2">
								<image src="http://static.hcoder.net/graceui/hb.png" style="width:100%; border-radius:5px;" mode="widthFix"></image>
							</view>
						</graceMaskView> -->
					</view>
				
					</view>
			</view>

			
		</view>
	</view>
</template>

<script>
import graceMaskView from "../../graceUI/components/graceMaskView.vue";
var loginRes, _self;
export default {
	
	data() {
		return {	
			staticUrl: this.staticUrl,
			show : false,
			dis: true,
			/* show2 : false, */
			
			storageData: {
				userId: 0,
				login: false
			
			},

			/* storageData: {}, */
			avatar: '',
			nickname: '',

			//分类信息
			categories: [
				{
					cateid: 0,
					name: '文章'
				},
				{
					cateid: 1,
					name: '关注'
				},
				{
					cateid: 2,
					name: '收藏'
				},
				{
					cateid: 3,
					name: '积分'
				}
				
			],
			// 当前选择的分类
			cateCurrentIndex: 0,
			articles: [],
			follows: []
		};
	},
	onLoad: function() {},
	onShow: function() {
		var _this = this;
		const loginKey = uni.getStorageSync('login_key');

		if (loginKey) {
			this.storageData = {
				login: loginKey.login,
				nickname: loginKey.nickname,
				avatar: loginKey.avatar,
				userId: loginKey.userId
			};
			uni.request({
				
				url: this.apiServer + '/article/user',
				method: 'GET',
				header: { 'content-type': 'application/x-www-form-urlencoded' },
				data: {
					userId: this.storageData.userId
				},
				success: res => {
					_this.articles = res.data.data;
				}
			});
			uni.request({
				
				url: this.apiServer + '/follow/list',
				method: 'GET',
				header: { 'content-type': 'application/x-www-form-urlencoded' },
				data: {
					fromUId: this.storageData.userId
				},
				success: res => {
					_this.follows = res.data.data;
				}
			});
		} else {
			this.storageData = {
				login: false
			};
		}

		uni.request({
			url: 'http://localhost:8080/api/user/' + uni.getStorageSync('login_key').userId,
			method: 'GET',
			header: { 'content-type': 'application/json' },
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
		handleTime: function(date) {
				var d = new Date(date);
				var year = d.getFullYear();
				var month = d.getMonth() + 1;
				var day = d.getDate() < 10 ? '0' + d.getDate() : '' + d.getDate();
				var hour = d.getHours() < 10 ? '0' + d.getHours() : '' + d.getHours();
				var minutes = d.getMinutes() < 10 ? '0' + d.getMinutes() : '' + d.getMinutes();
				var seconds = d.getSeconds() < 10 ? '0' + d.getSeconds() : '' + d.getSeconds();
				return year + '-' + month + '-' + day + ' ' + hour + ":" + minutes + ':' + seconds
			},
		tabChange: function(e) {
			// 选中的索引
			var index = e.currentTarget.dataset.index;
			// 具体的分类id
			var cateid = e.currentTarget.dataset.cateid;
			this.cateCurrentIndex = index;
			// 动态替换内容
			this.content = this.categories[index].name;
		},
		gotoDetail: function(aId) {
			uni.navigateTo({
				url: '../article_detail/article_detail?aId=' + aId + '&userId=' + this.storageData.userId
			});
		},
			showBanner : function(){
				this.show = true;
				this.dis = true;
			},
			closeBanner : function(){
				this.show = false;
				this.dis = false;
			},
			// 第2个演示 开启与关闭
			/* showBanner2 : function(){
				this.show2 = true;
			},
			closeBanner2 : function(){
				this.show2 = false;
			}, */
			tap2 : function(){
				uni.showToast({
					title:"您点击了红包图片",
					icon:"none"
				})
			}
		
		
	},
	components :{
			graceMaskView
		}
}
</script>

<style scoped>
/* 头像 */
.top {
	/* display: flex; */
	height: 70px;
	margin-top: 10px;
	/* border: 2px solid #007AFF; */
}
.avatar-box {
	display: flex;
	align-content: center;
	justify-content: center;
	/* flex: 1 1 30%; */
	margin-bottom: 10px;
	
	/* border: 1px solid #aaa; */
}
.top-name text{
	font-size: 20px;
    font-weight: 700;
	
}
.info-box {
	/* flex: 1 1 70%; */
	display: flex;
	align-items: center;
	justify-content: center;
	/* border: 1px solid blue; */
}
.info-box-s text{
	font-size: 20px;
}
/* 昵称 */
.top-name {
	display: flex;
	justify-content: center;
	/* border: 1px solid red; */
}
/*  个人设置*/
.btn {
	margin-left: 10px;
	/* border: 1px solid red; */
	color: rgb(133, 218, 70);
	height: 40px;
}
.btn text{
	font-size: 20px;
}

.c1 {
	margin-top: 10px;
	margin-bottom: 1px;
	white-space: nowrap;
	display: flex;
	
}
.c1-1 {
	width: 100%;
	height: 130px;
	display: inline-block;
}
.c1-1 image{
	width: 100%;
	height: 100%;
}



.middle {
	
	margin-top: 10px;
}

.content{
	/* border: 1px solid #2F2F2F; */
}

.list{
	
}
.list-item{
	height: 80px;
	display: flex;
	justify-content: space-between;
	align-items: center;
	/* border: 1px solid #007AFF;
}

.grace-tab-title{
	display: flex;
	/* border:  1px solid #10AEFF; */
}
  .list-title text{

	  font-size: 20px;
	  margin-left: 15px;
	  font-weight: 700;
  }
.grace-tab-box{
	display: flex;
	/* border:  1px solid #007AFF; */
}

.demo-content{
	text-align: center;
	margin-top: 10px;
	border: 1px;
}
</style>
