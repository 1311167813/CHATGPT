<template>
	<view class="content">
		<view class="list">
			<block v-for="(item,index) in list" :key="index">
				<view v-if="item.f" class="item-f">
					<image src="/static/img.jpg" mode="aspectFill"></image>
					<view>{{item.text}}</view>
				</view>
				<view v-else class="item-r">
					<view>{{item.text}}</view>
					<image src="/static/img.jpg" mode="aspectFill"></image>
				</view>
			</block>
		</view>
		<view class="input">
			<textarea v-model="text" fixed auto-height></textarea>
			<view @click="submit">发送</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list:[{
					f:true,
					text:'说点什么吧~'
				}],
				text:'',
			}
		},
		onLoad() {

		},
		methods: {
			// 发送
			submit(){
				let obj = {
					f:false,
					text:this.text
				}
				this.list.push(obj)
				this.text = ''
				uni.showLoading({
					title:'等待回复中'
				})
				uni.request({
					url:'https://api.openai.com/v1/completions',
					method:'POST',
					header:{
						'Content-Type': 'application/json',
						'Authorization':'Bearer 这里换成你的KEY'
					},
					data:{
						model:'text-davinci-003',
						prompt:obj.text,
						temperature:0,
						user:'这里填邮箱或不填'
					},
					success:(res => {
						uni.hideLoading()
						console.log(res);
						if(res.statusCode == 200){
							let obj = {
								f:true,
								text:res.data.choices[0].text
							}
							this.list.push(obj)
						} else {
							setTimeout(()=>{
								uni.showToast({
									title:'发送失败请重试~'
								})
							},300)
						}
					})
				})
			},
		}
	}
</script>

<style lang="scss" scoped>
	.list{
		width: 710rpx;
		padding: 20rpx;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		.item-f{
			display: flex;
			margin-bottom: 20rpx;
			image{
				height: 88rpx;
				width: 88rpx;
				border-radius: 8rpx;
				margin-right: 20rpx;
			}
			view{
				background-color: #fff;
				padding: 10rpx 20rpx;
				font-size: 34rpx;
				color: #333;
				border-radius: 12rpx;
				display: flex;
				align-items: center;
				justify-content: center;
				max-width: 450rpx;
			}
		}
		.item-r{
			display: flex;
			justify-content: flex-end;
			margin-left: 108rpx;
			margin-bottom: 20rpx;
			image{
				height: 88rpx;
				width: 88rpx;
				border-radius: 8rpx;
				margin-left: 20rpx;
			}
			view{
				background-color: #95EC69;
				padding: 10rpx 20rpx;
				font-size: 34rpx;
				color: #333;
				border-radius: 12rpx;
				display: flex;
				align-items: center;
				justify-content: center;
				max-width: 455rpx;
			}
		}
	}
	.input{
		background-color: #f6f6f6;
		position: fixed;
		bottom: 0;
		left: 0;
		width: 100%;
		padding-bottom: 50rpx;
		display: flex;
		align-items: center;
		border-top: 2rpx solid #ECECEC;
		padding-top: 20rpx;
		textarea{
			width: 450rpx;
			background-color: #FEFEFE;
			font-size: 28rpx;
			color: #666;
			padding: 20rpx 10rpx;
			border-radius: 12rpx;
			margin: 0 40rpx;
		}
		view{
			background-color: #E9E9E9;
			font-size: 32rpx;
			font-weight: bold;
			padding: 14rpx 60rpx;
			color: #07C160;
			border-radius: 12rpx;
		}
		view:active{
			background-color: #D2D2D2;
		}
	}
</style>
<style>
	page{
		background-color: #EDEDED;
	}
</style>
