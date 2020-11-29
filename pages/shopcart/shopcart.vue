<template>
	<view class="bg">
		<view class="data-nav">
			<view class="black">
				<p>共 {{totalNumber}} 种商品</p>
			</view>
			<view class="confirm-view">
				<tui-button margin="0 50rpx 0 30rpx" shape="circle" plain type="primary" width="170rpx" height="60rpx" @tap="changeMode">{{mode==='buy'?'管理':'完成'}}</tui-button>
			</view>
		</view>
		<view class="list-view">
			<view class="list-cell" v-for="item in shopcartList" :key="item.shopcartId">
				<radio style="line-height: 33vw;" class="mr10" @tap="selectProduct(item)" :checked="selectList.find(v=>v===item.shopcartId)" />
				<view :style="{background:`url(${item.productgVo.productVo.fileRecordList[0].fileFullPath}) no-repeat center center`}"
				 class="product-img" @tap="goProduct(item.productgVo.productVo.productId)"></view>
				<view class="product-right">
					<view @tap="goProduct(item.productgVo.productVo.productId)">
						<p class="fs16 black">{{item.productgVo.productVo.name}}</p>
						<p class="fs14 grey">{{item.productgVo.attrList.reduce((a,c)=>a+c.attrSonName,'')}}</p>
						<p class="fs14 red">￥{{item.productgVo.price}}</p>
					</view>
					<view style="display:flex;justify-content: flex-end;">
						<inputNumber @update="val=>changeNumber(val,item)" :number="item.cartNumber" :max="item.productgVo.totalStock" />
					</view>
				</view>
			</view>
			<noData />
		</view>
		<view class="operation-nav">
			<view>
				<label class="black" @tap="selectAllProduct">
					<radio :checked="selectAll" />全选</label>
			</view>
			<view class="confirm-view" v-if="mode==='buy'">
				<p class="fs18">合计： <text class="price-text">￥{{totalPrice}}</text></p>
				<tui-button margin="0 50rpx 0 30rpx" shape="circle" type="primary" width="170rpx" height="60rpx" @tap="goOrder">结算</tui-button>
			</view>
			<view class="confirm-view" v-if="mode==='manage'">
				<tui-button margin="0 50rpx 0 30rpx" shape="circle" type="danger" width="170rpx" :disabled="shopcartList.length===0"
				 @tap="clear" height="60rpx">清空</tui-button>
				<tui-button margin="0 50rpx 0 30rpx" shape="circle" type="primary" width="170rpx" height="60rpx" @tap="batch">删除</tui-button>
			</view>
		</view>
		<tui-modal :show="batchModal" @click="batchList" content="确定删除这些商品吗？" color="#333" :size="32" shape="circle"></tui-modal>
		<tui-modal :show="clearModal" @click="clearList" content="确定清空购物车吗？" color="#333" :size="32" shape="circle"></tui-modal>
	</view>
</template>

<script>
	import url from '../../api/index'
	import inputNumber from '../../components/inputNumber'
	import noData from '../../components/noData'
	import {
		mapGetters
	} from 'vuex'
	export default {
		components: {
			inputNumber,
			noData
		},
		data() {
			return {
				selectAll: false,
				batchModal: false,
				clearModal: false,
				selectList: [],
				shopcartList: [],
				mode: "buy"
			};
		},
		computed: {
			...mapGetters(["isLogin"]),
			totalNumber() {
				return this.shopcartList.length
			},
			totalPrice() {
				if (this.shopcartList.length === 0) return 0
				let data = []
				this.selectList.forEach(v => {
					data.push(this.shopcartList.find(item => item.shopcartId === v))
				})
				return data.reduce((a, c) => a + c.cartNumber * c.productgVo.price, 0).toFixed(2) - 0
			}
		},
		methods: {
			goOrder() {
				if (this.selectList.length === 0) {
					this.tui.toast('请至少选择1个商品', 3000);
					return
				}
				let list = []
				this.selectList.forEach(v => {
					const data = this.shopcartList.find(item => item.shopcartId === v)
					list.push(data)
				})
				this.$store.commit('setOrder', list)
				uni.navigateTo({
					url: '../order/order'
				})
			},
			selectProduct(row) {
				const index = this.selectList.findIndex(v => v === row.shopcartId)
				if (index !== -1) {
					this.selectList.splice(index, 1)
				} else {
					this.selectList.push(row.shopcartId)
				}
			},
			selectAllProduct() {
				if (this.selectAll) {
					this.selectAll = false
					this.selectList = []
				} else {
					this.selectAll = true
					this.shopcartList.forEach(v => {
						this.selectList.push(v.shopcartId)
					})
				}
			},
			changeNumber(val, row) {
				const index = this.shopcartList.findIndex(v => v.shopcartId === row.$orig.shopcartId)
				this.shopcartList[index].cartNumber = val
				this.tui.request(url.updateShopcart, 'PUT', this.shopcartList[index])
			},
			changeMode() {
				this.mode = this.mode === 'buy' ? 'manage' : 'buy'
			},
			getList() {
				this.tui.request(url.shopcartList, 'GET').then(res => {
					this.shopcartList = res.data
				})
			},
			goProduct(id) {
				uni.navigateTo({
					url: `../product/product?id=${id}`
				})
			},
			batch() {
				if (this.selectList.length === 0) {
					this.tui.toast('请至少选择1个商品', 3000);
					return
				}
				this.batchModal = true
			},
			batchList(e) {
				if (e.index === 1) {
					this.tui.request(url.batchDeleteShopcart, 'DELETE', {
						ids: this.selectList.join(",")
					}).then(res => {
						this.tui.toast('删除成功', 3000);
						this.selectList = []
						this.getList()
						this.batchModal = false
					})
				} else {
					this.batchModal = false
				}

			},
			clear() {
				this.clearModal = true
			},
			clearList(e) {
				if (e.index === 1) {
					this.tui.request(url.clearShopcart, 'DELETE').then(res => {
						this.tui.toast('清空成功', 3000);
						this.selectList = []
						this.getList()
						this.clearModal = false
					})
				} else {
					this.clearModal = false
				}

			}
		},
		onLoad() {
			if (this.isLogin) {
				this.getList()
			}
		},
		async onPullDownRefresh() {
			this.shopcartList = []
			await this.getList();
			uni.stopPullDownRefresh();
			this.tui.toast("刷新成功");
		},
	}
</script>

<style lang="scss" scoped>
	.bg {
		background-color: #f7f7f7;

		.operation-nav {
			position: absolute;
			bottom: 0;
			left: 0;
			width: 100vw;
			height: 88rpx;
			padding: 0 30rpx;
			display: flex;
			justify-content: space-between;
			align-items: center;
			border-top: 1px solid #ccc;
		}

		.list-view {
			margin: 88rpx 20rpx;
			height: calc(100vh - 176rpx);
			padding: 2rpx 0;

			.list-cell {
				margin-top: 20rpx;
				display: flex;
				align-items: center;
				padding: 16rpx;
				background-color: #fff;
				border-radius: 20rpx;

				.product-img {
					width: 33vw;
					height: 33vw;
					background-size: 30% 30%;
					border-radius: 20rpx;
					margin-right: 20rpx;
				}

				.product-right {
					height: 33vw;
					flex: 1;
					padding: 10rpx 0;
					display: flex;
					flex-direction: column;
					justify-content: space-between;
				}
			}
		}

		.data-nav {
			position: absolute;
			top: 0;
			left: 0;
			width: 100vw;
			height: 88rpx;
			padding: 0 30rpx;
			display: flex;
			justify-content: space-between;
			align-items: center;
			border-bottom: 1px solid #ccc;
		}

		.confirm-view {
			display: flex;
			align-items: center;

			.price-text {
				color: #FF0000;
				font-size: 28rpx;
			}
		}
	}
</style>
