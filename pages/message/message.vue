<template>
	<view class="container">
		<view class="top">
			<view class="card">
				<navigator url="interaction" hover-class="navigator-hover">
					<view class="image"><image src="../../static/hd.png"></image></view>
					<view class="character"><text>互动消息</text></view>
				</navigator>
			</view>

			<view class="card">
				<navigator url="others" hover-class="navigator-hover">
					<view class="image"><image src="../../static/qt.png"></image></view>
					<view class="character"><text>其他提醒</text></view>
				</navigator>
			</view>
		</view>

		<view class="main">
			<view class="main-top">
				<navigator url="../write/write" hover-class="navigator-hover">
				<view class="jianxin"><text>写文章</text></view>
				</navigator>
				<navigator url="../write/write" hover-class="navigator-hover">
				<view class="new-jianxin">
					<navigator url="newmessage" hover-class="navigator-hover"><text>新简信</text></navigator>
				</view>
				</navigator>
				
			</view>
			<!-- <view class="main-bottom">
				<view class="writeravatar">
					<image :src="jianxin.writeravatar"></image>
				</view>
			</view> -->
		</view>
	</view>
</template>

<script>
	export default{
		data(){
			return{
				/* jianxin:[
					{
						writeravatar:'https://upload.jianshu.io/users/upload_avatars/10849033/7278547c-f8ba-4a5a-a2ad-5dfd43abab81.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/240/h/240',
						writer:'lostdays',
						content:'哈哈哈哈哈哈哈哈哈哈呵'
					}
				] */
			}
		}
	}
</script>

<style>
.top {
	/* height: 50px; */
	display: flex;
	align-content: center;
	margin-top: 10px;
	/* border: 1px solid #007AFF; */
}

.card {
	margin-top: 5px;
	margin-bottom: 5px;
	flex: 1 1 33%;
	/* border: 1px solid black; */
}
.image {
	display: flex;
	justify-content: center;
	align-content: center;
}
.character {
	margin-top: 3px;
	margin-bottom: 3px;
	display: flex;
	justify-content: center;
	align-content: center;
	font-size: 10px;
}

.main{
	justify-content: space-between;
	margin-top: 20px;
	/* border: 1px solid red; */
}
.main-top{
	display: flex;
	align-content: center;
	justify-content: space-between;
}
.jianxin{
	margin-left: 5px;
	font-size: 20pt;
}
.new-jianxin{
	margin-right: 10px;
	margin-top: 6px;
	margin-right: 5px;
	font-size: 15pt;
	color: #DE533A;
}
image {
	height: 30px;
	width: 30px;
}
</style>

