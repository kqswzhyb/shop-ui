<template>
	<view>
		<view class="user-info">
			<view class="avatar-view" @tap="goLogin">
				<image class="avatar" src="" mode="aspectFit"></image>
			</view>

			<text @tap="goLogin">{{isLogin?userInfo.nickName:'请先登录'}}</text>
		</view>
		<tui-tabs :tabs="navbar" :currentTab="currentTab" selectedColor="#EB0909" sliderBgColor="#EB0909" @change="changeTab"></tui-tabs>
	</view>
</template>

<script>
	import {
		mapGetters
	} from 'vuex'
	export default {
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
	.user-info {
		width: 100vw;
		padding: 30rpx;
		display: flex;
		align-items: center;
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
</style>
