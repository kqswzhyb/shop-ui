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
</style>
