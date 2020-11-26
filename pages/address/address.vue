<template>
	<view class="bg">
		<view class="address-view">
			<tui-card :title="`地址`+i" v-for="item,i in addressList" :key="item.addressId">
				<template v-slot:body>
					<view class="tui-default">
						{{item.contactName}}——{{item.contactPhone}}
					</view>
				</template>
				<template v-slot:footer>
					<view class="tui-default">
						<view>
							<view>{{item.provinceName}} {{item.cityName}} {{item.areaName}}</view>
							<text>编辑</text>
						</view>
						<p>{{item.addressDetail}}</p>
					</view>
				</template>
			</tui-card>
		</view>
		<view class="no-data" v-if="addressList.length===0">
			<noData text="暂无收货地址" />
		</view>
		<view class="footer-nav">
			<tui-button shape="circle" type="primary" width="400rpx" height="90rpx" @tap="add">新增地址</tui-button>
		</view>
	</view>
</template>

<script>
	import noData from '../../components/noData'
	export default {
		components: {
			noData
		},
		data() {
			return {
				addressList: []
			};
		},
		methods:{
			add(){
				uni.navigateTo({
					url: '../AddressDetail/AddressDetail?mode=add'
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.bg {
		background-color: #f7f7f7;
	}
	.no-data {
		width:100vw;
		height:100vh;
		display: flex;
		justify-content: center;
		align-items: center;
	}
	.address-view{
		height: calc(100vh - 120rpx);
		width: 100vw;
	}
	.footer-nav {
		position: fixed;
		bottom: 0;
		left:0;
		width:100vw;
		height: 120rpx;
		display: flex;
		justify-content: center;
		align-items: center;
	}
</style>
