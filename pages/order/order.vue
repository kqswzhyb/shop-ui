<template>
	<view class="bg">
		<view class="address-card">
			<view class="fs14">{{detail.contactName}} <text class="fs12 ml15 grey">{{detail.contactPhone}}</text></view>
			<view class="fs12 black">{{detail.provinceName}} {{detail.cityName}} {{detail.areaName}}</view>
			<view class="fs12 black">{{detail.addressDetail}}</view>
		</view>
		<view class="list-cell" v-for="v in detail.orderDetailList" :key="v.productgId">
			<view :style="{background:`url(${v.imgUrl}) no-repeat center center`}" class="product-img" @tap="goProduct(v.productId)"></view>
			<view class="product-right">
				<view @tap="goProduct(v.productId)">
					<p class="fs16 radio">{{v.productName}}</p>
					<p class="fs14 grey">{{v.attrs}}</p>
					<p class="fs14 red">￥{{v.originPrice}} X{{v.productNum}}</p>
				</view>
			</view>
		</view>
		<p class="fs14 px15">运费：<text class="red mr10">￥{{detail.freight}}</text> 总额：<text class="red">￥{{detail.productTotalAmount}}</text>
			<text class="grey" v-if="detail.orderStatus">实付额：￥{{detail.actualPayAmount}}</text></p>
		<view class="submit-view">
			<tui-button margin="0 50rpx 0 30rpx" shape="circle" type="primary" width="170rpx" height="60rpx" @tap="submitOrder">提交订单</tui-button>
		</view>

	</view>
</template>

<script>
	import {
		mapGetters
	} from 'vuex'
	import url from '../../api/index'
	export default {
		data() {
			return {
				detail: {
					orderId: "",
					userId: '',
					orderCode: '',
					userName: '',
					provinceId: '',
					cityId: '',
					areaId: '',
					provinceName: "",
					cityName: "",
					areaName: "",
					addressDetail: "",
					contactName: "",
					contactPhone: "",
					freight: "",
					productTotalAmount: 0,
					orderTotalAmount: 0,
					actualPayAmount: 0,
					remark: "",
					paymentMethod: "",
					isInvoice: "",
					orderStatus: "",
					orderDetailList: []
				}
			};
		},
		computed: {
			...mapGetters(["order", "address", "userInfo"]),
		},
		watch: {
			order: {
				handler(val) {
					if (val.length !== 0) {
						this.detail.orderDetailList = val.map(v => ({
							productId: v.productgVo.productVo.productId,
							productgId: v.productgVo.productgId,
							productCode: v.productgVo.productVo.productCode,
							productName: v.productgVo.productVo.name,
							brandId: v.productgVo.productVo.brandId,
							originPrice: v.productgVo.price,
							productNum: v.cartNumber,
							productUnit: v.productgVo.productVo.productUnit,
							attrs: v.productgVo.attrList.reduce((a, c) => a + c.attrSonName, ''),
							imgUrl: v.productgVo.productVo.fileRecordList[0].fileFullPath
						}))
					}
				},
				immediate: true
			},
			'detail.orderDetailList': {
				handler(val) {
					if (val) {
						this.detail.productTotalAmount = val.reduce((a, c) => a + c.originPrice * c.productNum, 0)
						if (this.detail.productTotalAmount >= 100) {
							this.detail.freight = 0
						} else {
							this.detail.freight = 10
						}
					}
				},
				immediate: true,
				deep: true
			}
		},
		methods: {
			submitOrder() {
				this.detail.isInvoice = "0"
				this.detail.paymentMethod = "0"
				this.detail.actualPayAmount = 0
				this.tui.request(url.addOrder, "POST", this.detail).then(res => {
					this.tui.toast('已提交订单请尽快付款', 3000);
				})
			}
		},
		onLoad() {
			if (this.address.length !== 0) {
				const data = this.address[0]
				this.detail.contactName = data.contactName
				this.detail.contactPhone = data.contactPhone
				this.detail.provinceId = data.provinceId
				this.detail.cityId = data.cityId
				this.detail.areaId = data.areaId
				this.detail.provinceName = data.provinceName
				this.detail.cityName = data.cityName
				this.detail.areaName = data.areaName
				this.detail.addressDetail = data.addressDetail
				this.detail.userId = this.userInfo.userId
				this.detail.userName = this.userInfo.userName
			}
		}
	}
</script>

<style lang="scss" scoped>
	.bg {
		background-color: #f7f7f7;
		padding-top: 40rpx;

		.address-card {
			border-radius: 30rpx;
			background-color: #fff;
			padding: 30rpx;
			margin-bottom: 40rpx;
		}
	}

	.list-cell {
		margin-top: 20rpx;
		display: flex;
		align-items: center;
		padding: 16rpx;
		background-color: #fff;
		border-radius: 20rpx;

		.product-img {
			width: 22vw;
			height: 22vw;
			background-size: 20% 20%;
			border-radius: 20rpx;
			margin-right: 20rpx;
		}

		.product-right {
			height: 23vw;
			flex: 1;
			padding: 10rpx 0;
			display: flex;
			flex-direction: column;
			justify-content: space-between;
		}

		.submit-view {
			width: 100%;
			margin-top: 40rpx;
			height: 80rpx;
			display: flex;
			align-items: center;
			justify-content: flex-end;
		}
	}
</style>
