<template>
	<view class="detail">
		<view class="title">{{detail.title}}</view>
		<view class="info">
			<view class="author">编辑：{{detail.author}}</view>
			<view class="time">发布日期：{{detail.posttime}}</view>
		</view>
		<!-- 两种渲染方式v-html和富文本 二选一即可 -->
		<rich-text class="content" :nodes="detail.content"></rich-text>
		<!-- <view class="content" v-html="detail.content" /> -->

		<view class="description">
			声明:本站的内容均采集与腾讯新闻，如果侵权请联系管理(513894357@qq.com)进行整改删除，本站进行了内容采集不代表本站及作者观点，若有
			侵犯请及时联系管理员，谢谢您的支持。
		</view>
	</view>
</template>

<script>
	import {
		parseTime
	} from "@/utils/tool.js"
	export default {
		data() {
			return {
				// 传递过来的cid和id
				options: null,
				// 新闻详情数据
				detail: {}
			};
		},
		onLoad(e) {
			// 保存传递过来的cid和id，用于发请求
			this.options = e;
			// 发请求
			this.getDetail();
		},
		methods: {
			getDetail() {
				uni.request({
					url: "https://ku.qingnian8.com/dataApi/news/detail.php",
					method: 'GET',
					data: this.options,
					success: (res) => {
						if (res.statusCode === 200) {
							// 正则匹配修改数据
							res.data.content = res.data.content.replace(/<img/gi,
								"<img style='max-width:100%'");
							// 格式化时间戳
							res.data.posttime = parseTime(res.data.posttime)
							// 保存数据
							this.detail = res.data;
							// 保存浏览历史
							this.saveHistory();
							// 重新设置uni导航栏标题
							uni.setNavigationBarTitle({
								title: this.detail.title
							})
						}
					}
				})
			},
			saveHistory() {
				// 先获取历史记录，如果不存在就赋值为空数组
				let historyArr = uni.getStorageSync("historyArr") || [];
				// 不需要传递整个item整理参数
				let item = {
					id: this.detail.id,
					classid: this.detail.classid,
					picurl: this.detail.picurl,
					title: this.detail.title,
					lookTime: parseTime(Date.now())
				}
				// 判断历史记录的文章和现在正在阅读的文章id是否相同
				let index = historyArr.findIndex(i => {
					return i.id === this.detail.id
				})
				// index>=0说明该文章已存在于历史记录中，index==-1则证明不存
				if (index >= 0) {
					// 删掉重复的文章
					historyArr.splice(index, 1)
				}
				// 往数组最前端插入数据
				historyArr.unshift(item)
				// 只保存10条数据
				historyArr = historyArr.slice(0, 10)
				// 保存到localStorage里
				uni.setStorageSync("historyArr", historyArr)

			}
		},
	}
</script>

<style lang="scss">
	.detail {
		padding: 30rpx;

		.title {
			font-size: 46rpx;
			color: #333;
		}

		.info {
			background-color: #F6F6F6;
			padding: 20rpx;
			font-size: 22rpx;
			color: #666;
			display: flex;
			justify-content: space-between;
			margin: 30rpx 0;
		}

		.content {
			padding-bottom: 50rpx;
			// 微信小程序不兼容
			/* /deep/ img {
				max-width: 100%;
			} */
		}

		.description {
			background-color: #EFE0F0;
			font-size: 26rpx;
			padding: 20rpx;
			color: #F89898;
			line-height: 1.8em;
		}
	}
</style>