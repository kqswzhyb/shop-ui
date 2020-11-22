<template>
	<view class="bg">
		<swiper class="swiper" :indicator-dots="indicatorDots" :autoplay="autoplay" :interval="interval" :duration="duration">
			<swiper-item v-for="item in product.fileRecordList" :key="item.fileId">
				<view>
					<image class="swiper-img" mode="scaleToFill" lazy-load :src="item.fileFullPath"></image>
				</view>
			</swiper-item>
		</swiper>
		<view class="product-info">
			<p class="product-price fs10">￥<text class="fs18">{{priceInterval}}/{{product.productUnit}}</text></p>
			<p class="product-name">{{product.name}}[{{product.brand.name}}]</text></p>
		</view>
		<view class="product-info">
			<p class="product-name" @tap="bottomPopup=true">已选: {{attrList}}{{number}}{{product.productUnit}}</text></p>
		</view>
		<view class="footer">
			<div class="footer-button">
				<tui-button type="danger" height="66rpx">加入购物车</tui-button>
			</div>
			<div class="footer-button">
				<tui-button type="warning" height="66rpx">立即购买</tui-button>
			</div>
		</view>
		<tui-drawer mode="bottom" :visible="bottomPopup" @close="closeDrawer">
		<view class="draw-view">
			<view class="flex-between">
				<p class="product-price fs12">￥<text class="fs18">{{totalPrice}}</text></p>
				<tui-icon name="shut" size="26" style="padding: 5px;" color="#333" @tap.native="closeDrawer"/>
			</view>
			
			<view class="number-line my15">
				<text class="product-name">数量：</text>
				<inputNumber @update="updateNumber"/>
			</view>
			<view class="my15" v-for="item in product.attrBaseList" :key="item.attrId">
				<p class="product-name">{{item.attrName}}：</p>
				<view class="attr-list">
					<text class="attr-cell" v-for="v in item.attrSonList" :key="v.attrSonId">{{v.name}}</text>
				</view>
			</view>
		</view>
		</tui-drawer>
	</view>
</template>

<script>
	import url from '../../api/index.js'
	import inputNumber from '../../components/inputNumber'
	export default {
		components:{
			inputNumber
		},
		data() {
			return {
				product: {},
				bottomPopup:false,
				indicatorDots: true,
				autoplay: true,
				interval: 2000,
				duration: 500,
				attrList: '',
				productgId: '',
				number: 1
			};
		},
		computed: {
			priceInterval() {
				if (this.product.productgList) {
					const priceList = this.product.productgList.map(v => v.price)
					const min = Math.min(...priceList)
					const max = Math.max(...priceList)
					if (min === max) {
						return min
					}
					return `${min} ~ ${max}`
				}
				return '暂无定价'
			},
			totalPrice() {
				if(this.productgId) {
					return this.product.productgList.find(v=>v.productgId===this.productgId).price *this.number
				}
				return ''
			}
		},
		methods: {
			updateNumber(val) {
				this.number=val
			},
			closeDrawer() {
				this.bottomPopup=false
			},
			getPorductList(id) {
				return new Promise((resolve) => {
					this.tui.request(url.productList, "get", {
						saleStatus: '1',
						productId: id
					}).then(res => {
						this.product = res.data.records[0]
						resolve()
					})
				})
			},
		},
		async onLoad(option) {
			this.number = 1
			await this.getPorductList(option.id)
			const productg = this.product.productgList[0]
			this.productgId = productg.productgId
			this.attrList = productg.attrList.reduce((a, c) => a + c.attrSonName, '')
			if (this.attrList) {
				this.attrList += ','
			}
		}
	}
</script>

<style lang="scss" scoped>
	.bg {
		background-color: #f7f7f7;
		min-height: 100vh;

		.swiper {
			width: 100vw;
			height: 100vw;
			background-color: #fff;

			.swiper-img {
				width: 100vw;
				height: 100vw;
			}
		}

		.product-info {
			margin-top: 40rpx;
			border-radius: 30rpx;
			padding: 30rpx;
			background-color: #fff;
			
		}
		.product-name {
			color: #333;
		}
		.product-price {
			color: #FF0000;
		}
	}

	.footer {
		width: 100vw;
		height: 88rpx;
		display: flex;
		justify-content: space-around;
		align-items: center;
		position: fixed;
		bottom: 0;
		left: 0;
		z-index: 999999;
		background-color: #fff;

		.footer-button {
			width: 45vw;
		}
	}
	.draw-view {
		padding: 44rpx 44rpx  168rpx;
		.number-line {
			display: flex;
			align-items: center;
			justify-content: space-between;
		}
		.attr-list {
			display: flex;
			flex-wrap: wrap;
			.attr-cell {
				margin: 20rpx;
				padding: 9rpx 24rpx;;
				border:1px solid #ccc;
				border-radius: 50rpx;
				display: flex;
				justify-content: center;
				align-items: center;
			}
		}
	}
</style>
