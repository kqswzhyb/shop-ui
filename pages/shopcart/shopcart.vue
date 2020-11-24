<template>
	<view class="bg">
		<view class="data-nav">
			<view class="radio">
				<p>共 {{totalNumber}} 种商品</p>
			</view>
			<view class="confirm-view">
				<tui-button margin="0 50rpx 0 30rpx" shape="circle" plain type="primary" width="170rpx" height="60rpx" @tap="changeMode">{{mode==='buy'?'管理':'完成'}}</tui-button>
			</view>
		</view>
		<view class="list-view">
			<view class="list-cell" v-for="item in shopcartList" :key="item.shopcartId">
				<radio style="line-height: 33vw;" class="mr10" :checked="selectList.find(v=>v===item.shopcartId)" />
				<view  :style="{background:`url(${item.productgVo.productVo.fileRecordList[0].fileFullPath}) no-repeat center center`}" class="product-img"></view>
				<view class="product-right">
					<view>
						<p class="fs16 radio">{{item.productgVo.productVo.name}}</p>
						<p class="fs14 grey">{{item.productgVo.attrList.reduce((a,c)=>a+c.attrSonName,'')}}</p>
					</view>
					<view style="display:flex;justify-content: flex-end;">
						<inputNumber @update="val=>item.cartNumber=val" :number="item.cartNumber" :max="item.productgVo.totalStock" />
					</view>
				</view>
			</view>
		</view>
		<view class="operation-nav">
			<view>
				<label class="radio">
					<radio :checked="selectAll" />全选</label>
			</view>
			<view class="confirm-view" v-if="mode==='buy'">
				<p class="fs18">合计： <text class="price-text">￥{{totalPrice}}</text></p>
				<tui-button margin="0 50rpx 0 30rpx" shape="circle" type="primary" width="170rpx" height="60rpx">结算</tui-button>
			</view>
			<view class="confirm-view" v-if="mode==='manage'">
				<tui-button margin="0 50rpx 0 30rpx" shape="circle" type="primary" width="170rpx" height="60rpx">删除</tui-button>
			</view>
		</view>
	</view>
</template>

<script>
	import url from '../../api/index'
	import inputNumber from '../../components/inputNumber'
	export default {
		components: {
			inputNumber
		},
		data() {
			return {
				selectAll: false,
				selectList: [],
				totalPrice: 0,
				shopcartList: [],
				mode: "buy"
			};
		},
		computed: {
			totalNumber() {
				return this.shopcartList.length
			}
		},
		methods: {
			changeMode() {
				this.mode = this.mode === 'buy' ? 'manage' : 'buy'
			}
		},
		onLoad() {
			this.tui.request(url.shopcartList, 'get').then(res => {
				this.shopcartList = res.data
				console.log(res.data)
			})
		}
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
			min-height: calc(100vh - 176rpx);
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
					height:33vw;
					flex:1;
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

		.radio {
			color: #333;
		}

		.grey {
			color: #888;
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
