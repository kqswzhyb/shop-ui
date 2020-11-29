<template>
	<view class="bg">
		<view class="user-info">
			<view style="display: flex;align-items: center;">
				<view class="avatar-view" @tap="goLogin">
					<image class="avatar" src="" mode="aspectFit"></image>
				</view>
				
				<text @tap="goLogin">{{isLogin?userInfo.nickName:'请先登录'}}</text>
			</view>
			<tui-icon name="house" size="25" class="mr40" color="#fff" @tap="goAddress"/>
			
		</view>
		<tui-tabs :tabs="navbar" :currentTab="currentTab" selectedColor="#EB0909" sliderBgColor="#EB0909" @change="changeTab"></tui-tabs>
		<view class="list-view" v-for="item in orderList" :key="item.orderId">
			<view class="list-cell" v-for="v in orderDetailList" :key="v.orderDetailId">
				<view :style="{background:`url(${v.imgUrl}) no-repeat center center`}"
				 class="product-img" @tap="goProduct(v.productId)"></view>
				<view class="product-right">
					<view @tap="goProduct(v.productId)">
						<p class="fs16 radio">{{v.productName}}</p>
						<p class="fs14 grey">{{v.attrs}}</p>
						<p class="fs14 red">￥{{v.originPrice}} X{{v.productUnit}}</p>
					</view>
				</view>
			</view>
			<p>总额：<text>{{item.productTotalAmount}}</text> 实付额：<text>{{item.actualPayAmount}}</text></p>
		</view>
		<view class="order-list">
			<noData/>
		</view>
	</view>
</template>

<script>
	import {
		mapGetters
	} from 'vuex'
	import noData from '../../components/noData'
	export default {
		components:{
			noData
		},
		data() {
			return {
				currentTab: 0,
				navbar: [{
					name: "待付款"
				}, {
					name: "待发货"
				},{
					name: "待收货"
				}, {
					name: "已完成"
				}],
				orderList:[]
			};
		},
		computed: {
			...mapGetters(['isLogin', "userInfo"])
		},
		onLoad() {},
		methods: {
			goAddress() {
				if (this.isLogin) {
					uni.navigateTo({
						url: '../address/address'
					})
				}
			},
			changeTab(val) {
				this.currentTab = val.index
			},
			goProduct(id) {
				uni.navigateTo({
					url: `../product/product?id=${id}`
				})
			},
			goLogin() {
				if (!this.isLogin) {
					uni.navigateTo({
						url: '../auth/login/login'
					})
				}
			}
		}
	}
</script>

<style lang="scss" scoped>
	.bg {
		background-color: #f7f7f7;
	}
	.user-info {
		width: 100vw;
		padding: 30rpx;
		display: flex;
		align-items: center;
		justify-content: space-between;
		background-color: #f50;

		.avatar-view {
			margin-right: 30rpx;
			width: 20vw;
			height: 20vw;
			border-radius: 50%;
			border: 1px solid #eee;
		}

		.avatar {
			width: 20vw;
			height: 20vw;

		}
	}
	.order-list {
		height: calc(100vh - 294rpx);
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
</style>
