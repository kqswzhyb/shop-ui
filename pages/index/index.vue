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
			<swiper-item>
				<view>
					<image class="swiper-img" mode="widthFix" src="https://m.360buyimg.com/mobilecms/s700x280_jfs/t1/140782/19/11070/138601/5f8949c0E2c2d0880/894fb1feafe6071b.jpg!q70.jpg.dpg"></image>
				</view>
			</swiper-item>
			<swiper-item>
				<view>
					<image class="swiper-img" mode="widthFix" src="https://m.360buyimg.com/mobilecms/s700x280_jfs/t1/128315/14/10190/87640/5f3e70a7E331f3a92/75b16a93d947e448.jpg!cr_1125x445_0_171!q70.jpg.dpg"></image>
				</view>
			</swiper-item>
			<swiper-item>
				<view>
					<image class="swiper-img" mode="widthFix" src="https://m.360buyimg.com/mobilecms/s700x280_jfs/t1/123859/17/19175/195983/5fb4c8efEca5113eb/04740e3305e72122.jpg!q70.jpg.dpg"></image>
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
				productList:[]
			}
		},
		computed: {
			...mapGetters(['location'])
		},
		onLoad() {
			this.getPorductList()
		},
		methods: {
			getPorductList() {
				this.tui.request(url.productList,"get").then(res=>{
					this.productList=res.data.records
					console.log(this.productList)
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
			width:100%;
			.swiper-img {
				width:100%;
			}
		}
		.product-list {
			margin-top:40rpx;
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
