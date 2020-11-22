<template>
	<view>
		<view class="product-list">
			<view v-for="(item,index) in productList" :key="item.productId" class="product-cell" @tap="goProduct(item.productId)">
				<image :src="item.fileRecordList[0].fileFullPath" class="product-img" mode="aspectFit"></image>
				<p class="fs14 px10">{{item.name}}</text>
					<p class="fs14 px10" style="color:#FF0000;"><span class="fs16">￥</span>{{item.productgList[0].price}}</p>
			</view>
		</view>
		<tui-loadmore v-if="loading"></tui-loadmore>
		<tui-nomore v-if="!pullUpOn"></tui-nomore>
	</view>
</template>

<script>
	import url from '../../api/index.js'
	export default {
		data() {
			return {
				page: {
					pageSize: 5,
					current: 1,
					total: 0,
				},
				name: '',
				productList: [],
				loading: false,
				pullUpOn: true
			};
		},
		methods: {
			getPorductList() {
				return new Promise((resolve) => {
					this.tui.request(url.productList, "get", {
						saleStatus: '1',
						name: this.name,
						...this.page
					}).then(res => {
						this.productList = this.productList.concat(res.data.records)
						this.page.total = res.data.total
						resolve()
					})
				})
			},
			goProduct(id) {
				uni.navigateTo({
					url: `../product/product?id=${id}`
				})
			},
		},
		onLoad(option) {
			this.name = option.name
			this.getPorductList()
		},
		//页面相关事件处理函数--监听用户下拉动作
		async onPullDownRefresh() {
			this.productList = []
			this.page.current = 1;
			await this.getPorductList();
			this.pullUpOn = true;
			this.loading = false;
			uni.stopPullDownRefresh();
			this.tui.toast("刷新成功");
		},

		// 页面上拉触底事件的处理函数
		async onReachBottom() {
			if (!this.pullUpOn) return;
			this.loading = true;
			if (this.page.pageSize * this.page.current >= this.page.total) {
				this.loading = false;
				this.pullUpOn = false;
			} else {
				this.page.current++
				await this.getPorductList()
				this.loading = false;
			}
		}
	}
</script>

<style lang="scss" scoped>
	.product-list {
		width: 100%;
		display: flex;
		align-items: center;
		flex-wrap: wrap;
		margin-bottom: 56rpx;

		.product-cell {
			margin-top: 40rpx;
			margin-left: calc(4vw - 6px);
			margin-right: calc(4vw - 6px);
			border: 1px solid #eee;

			.product-img {
				width: 44vw;
				height: 44vw;
			}
		}
	}
</style>
