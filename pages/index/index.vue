<template>
	<view class="content">
		<view class="header-view">
			<view>
				<tui-icon name="position" size="20" class="ml10" color="#fff" />
				<text class="fs14 mx5 white">{{location.city||'无法定位'}}</text>
			</view>

			<input class="uni-input" confirm-type="search" placeholder="搜索产品" />
		</view>
		<swiper class="swiper" :indicator-dots="indicatorDots" :autoplay="autoplay" :interval="interval" :duration="duration">
			<swiper-item v-for="item in bannerList" :key="item.bannerId">
				<view >
					<image class="swiper-img" mode="widthFix" :src="item.fileRecordList[0].fileFullPath"></image>
				</view>
			</swiper-item>
		</swiper>
		<view class="product-list">
			<view v-for="item in productList.slice(0,6)" :key="item.productList" class="product-cell">
				<image :src="item.fileRecordList[0].fileFullPath" class="product-img" mode="aspectFit"></image>
				<p class="fs14 px10">{{item.name}}</text>
					<p class="fs14 px10" style="color:#FF0000;"><span class="fs16">￥</span>{{item.productgList[0].price}}</p>
			</view>
		</view>
	</view>
</template>

<script>
	import {
		mapGetters
	} from 'vuex'
	import url from '../../api/index.js'
	export default {
		data() {
			return {
				indicatorDots: true,
				autoplay: true,
				interval: 2000,
				duration: 500,
				productList: [],
				bannerList: []
			}
		},
		computed: {
			...mapGetters(['location'])
		},
		onLoad() {
			this.getPorductList()
			this.getBannerList()
		},
		methods: {
			getPorductList() {
				this.tui.request(url.productList, "get",{
					saleStatus: '1'
				}).then(res => {
					this.productList = res.data.records
				})
			},
			getBannerList() {
				this.tui.request(url.bannerList, "get", {
					place: 'home'
				}).then(res => {
					this.bannerList = res.data.records.sort((a, b) => Number(a.sort) - Number(b.sort))
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.content {
		.header-view {
			position: fixed;
			top: 0;
			display: flex;
			justify-content: space-around;
			align-items: center;
			width: 100vw;
			height: 108rpx;
			background-color: #d43c33;
			z-index: 99;

			.uni-input {
				width: calc(100% - 270rpx);
				margin-right: 50rpx;
				background-color: #fff;
				border-radius: 20rpx;
				font-size: 26rpx;
				padding-left: 20px;
			}
		}

		.swiper {
			margin-top: 108rpx;
			width: 100%;

			.swiper-img {
				width: 100%;
			}
		}

		.product-list {
			margin-top: 40rpx;
			width: 100%;
			display: flex;
			justify-content: space-around;
			align-items: center;

			.product-cell {
				border: 1px solid #eee;

				.product-img {
					width: 30vw;
					height: 30vw;
				}
			}
		}
	}
</style>
