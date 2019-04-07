<template>
	<view class="container">
		<view class="head">
			<image src="../../static/shu.jpg"></image>
		</view>
		<view class="title">
			<input type="text" maxlength="20" placeholder="请输入标题" />
		</view>
		<view class="main-body">
			<input type="text" maxlength="-1" placeholder="请输入正文"/>
		</view>
		<view class="release">
			<text>发布</text>
		</view>
	</view>
</template>

<script>
</script>

<style>
	.container{
	width: 100%;
	flex-direction: column;
		
	}
	.title{
		font-size: 14pt;
		border-bottom: 1px dashed #aaa ;
		margin-bottom: 5px;
		text-align: center;
		
		
	}
	.head{
		margin-left: 27px;
		margin-top: 15px;
		width: 100%;
		
	}
	.head a{
		
		width: 100%;
		height:10px;
	}
	.main-body{
		margin-left: 5px;
		font-size: 12px;
		font-weight: 200;
		
	}
	.release{
		height: 20px;
		width: 50px;
		display: flex;
		justify-content: center;
		align-content: center;
		font-size: 11pt;
		color: #DE533A;
		float: right;
		margin-top: -52px;
	}
	
</style>