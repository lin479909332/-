<template>
	<view class="home">
		<scroll-view scroll-x class="navscroll">
			<view class="item" :class="navIndex == index ? 'active':''" v-for="(item,index) in navArr" :key="item.id"
				@click="clickNav(index,item.id)">{{item.classname}}</view>
		</scroll-view>
		<view class="content">
			<view class="row" v-for="(item,index) in newsArr" :key="item.id">
				<newsbox :item="item" @click.native="goDetail(item)"></newsbox>
			</view>
		</view>
		<view class="nodata" v-if="!newsArr.length">
			<image src="../../static/images/nodata.png" mode="widthFix"></image>
		</view>
		<view class="loading" v-show="newsArr.length">
			<view v-show="loading==1">数据加载中...</view>
			<view v-show="loading==2">没有更多了~~~</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 导航列表数据index
				navIndex: 0,
				// 导航列表数据数组
				navArr: [],
				// 新闻列表数据数组
				newsArr: [],
				// 新闻列表页码
				currentPage: 1,
				// loading状态 0默认 1数据加载中 2没有更多了
				loading: 0,
				// 新闻类别id,默认是国内的
				cid: 50
			}
		},
		onLoad() {
			// 获取导航列表数据
			this.getNavData();
			// 获取新闻列表数据
			this.getNewsData();
		},
		// 触底
		onReachBottom() {
			// 没有数据后就不再发送请求了
			if (this.loading === 2) {
				return;
			}
			this.currentPage++;
			this.loading = 1;
			this.getNewsData();
		},
		methods: {
			// 点击nav列表
			clickNav(index,cid) {
				// 切换高亮index
				this.navIndex = index;
				// 重置页码
				this.currentPage = 1;
				// 重置新闻列表
				this.newsArr = [];
				// 重置加载状态
				this.loading = 0;
				// 切换新的新闻类别id
				this.cid = cid
				// 重新根据nav的id获取新闻列表数据
				this.getNewsData(this.cid);
			},

			// 获取导航列表数据
			getNavData() {
				uni.request({
					url: "https://ku.qingnian8.com/dataApi/news/navlist.php",
					method: "GET",
					success: (res) => {
						if (res.statusCode === 200) {
							this.navArr = res.data
						}
					}
				})
			},

			// 获取新闻列表数据
			getNewsData(cid) {
				uni.request({
					url: "https://ku.qingnian8.com/dataApi/news/newslist.php",
					method: "GET",
					data: {
						cid: this.cid,
						page: this.currentPage
					},
					success: (res) => {
						if (res.statusCode === 200) {
							// 没有新数据了
							if (res.data.length === 0) {
								// 切换loading状态
								this.loading = 2
							}
							// 拼接数组
							this.newsArr = [...this.newsArr, ...res.data]
						}
					}
				})
			},
			
			// 跳转详情页
			goDetail(item){
				uni.navigateTo({
					url:`/pages/detail/detail?$cid=${item.classid}&id=${item.id}`
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.home {
		.navscroll {
			height: 100rpx;
			background: #F7F8FA;
			white-space: nowrap;
			position: fixed;
			top: var(--window-top);
			left: 0;
			z-index: 10;

			/deep/ ::-webkit-scrollbar {
				width: 4px !important;
				height: 1px !important;
				overflow: auto !important;
				background: transparent !important;
				-webkit-appearance: auto !important;
				display: block;
			}

			.item {
				font-size: 40rpx;
				display: inline-block;
				line-height: 100rpx;
				padding: 0 30rpx;
				color: #333;

				&.active {
					color: #31c27c;
				}
			}
		}

		.content {
			padding: 30rpx;
			padding-top: 100rpx;

			.row {
				border-bottom: 1px solid #efefef;
				padding: 20rpx 0;
			}
		}

		.nodata {
			display: flex;
			justify-content: center;

			image {
				width: 360rpx;
			}
		}

		.loading {
			text-align: center;
			font-size: 26rpx;
			color: #888;
			line-height: 2em;
		}
	}
</style>