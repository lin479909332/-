<template>
	<view class="user">
		<view class="top">
			<image src="../../static/images/history.png" mode="aspectFill"></image>
			<text>浏览历史</text>
		</view>
		<view class="content">
			<view class="row" v-for="item in listArr">
				<newsbox :item="item" @click.native="goDetail(item)"></newsbox>
			</view>
		</view>
		<view class="nohistory" v-if="!listArr.length">
			<image src="../../static/images/nohis.png" mode="widthFix"></image>
			<text>暂无浏览历史记录</text>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				listArr: []
			};
		},
		onShow() {
			this.getHistory()
		},
		methods: {
			getHistory() {
				let historyArr = uni.getStorageSync("historyArr") || []
				this.listArr = historyArr
				console.log(this.listArr);
			},
			goDetail(item) {
				uni.navigateTo({
					url: `/pages/detail/detail?$cid=${item.classid}&id=${item.id}`
				})
			}
		}
	}
</script>

<style lang="scss">
	.user {
		.top {
			padding: 50rpx 0;
			background-color: #f8f8f8;
			color: #666;
			display: flex;
			flex-direction: column;
			justify-content: center;
			align-items: center;

			image {
				width: 150rpx;
				height: 150rpx;
			}

			text {
				font-size: 38rpx;
				padding-top: 20rpx;
			}
		}

		.content {
			padding: 30rpx;

			.row {
				border-bottom: 1px solid #efefef;
				padding: 20rpx 0;
			}
		}

		.nohistory {
			display: flex;
			flex-direction: column;
			justify-content: center;
			align-items: center;

			image {
				width: 450rpx;
			}

			text {
				font-size: 26rpx;
				color: #888;
				line-height: 2em;
			}
		}
	}
</style>