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
		<p>总额：<text>{{item.productTotalAmount}}</text> 实付额：<text>{{item.actualPayAmount}}</text></p>
	</view>
</template>

<script>
	import {
		mapGetters
	} from 'vuex'
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
					productTotalAmount: "",
					actualPayAmount: "",
					remark: "",
					paymentMethod: "",
					isInvoice: "",
					orderStatus: "",
					orderDetailList: []
				}
			};
		},
		computed: {
			...mapGetters(["order", "address"]),
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
			}
		},
		methods: {},
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
	}
</style>
