<template>
	<view class="bg">
		<view class="address-view">
			<tui-card :title="`地址`+i" class="mt20 px1" v-for="item,i in addressList" :key="item.addressId">
				<template v-slot:body>
					<view class="tui-default px10">
						{{item.contactName}} —— {{item.contactPhone}}
					</view>
				</template>
				<template v-slot:footer>
					<view class="tui-default px10">
						<view class="pca">
							<view>{{item.provinceName}} {{item.cityName}} {{item.areaName}}</view>
							<view>
								<text class="edit-button" @tap="showEdit(item.addressId)">编辑</text>
								<text class="delete-button" @tap="showDelete(item.addressId)">删除</text>
							</view>
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
		<tui-modal :show="deleteModal" @click="doDelete" content="确定删除这个地址吗？" color="#333" :size="32" shape="circle"></tui-modal>
	</view>
</template>

<script>
	import url from '../../api/index'
	import noData from '../../components/noData'
	export default {
		components: {
			noData
		},
		data() {
			return {
				addressList: [],
				deleteModal: false,
				current: ''
			};
		},
		methods: {
			add() {
				uni.navigateTo({
					url: '../AddressDetail/AddressDetail?mode=add'
				})
			},
			getList() {
				this.tui.request(url.addressList, "GET").then(res => {
					this.addressList = res.data
				})
			},
			showDelete(id) {
				this.current = id
				this.deleteModal = true
			},
			showEdit(id) {
				uni.navigateTo({
					url: `../AddressDetail/AddressDetail?mode=edit&id=${id}`
				})
			},
			doDelete(e) {
				if (e.index === 1) {
					this.tui.request(url.deleteAddress, 'DELETE', {
						id: this.current
					}).then(res => {
						this.tui.toast('删除成功', 3000);
						this.current = ''
						this.getList()
						this.deleteModal = false
					})
				} else {
					this.deleteModal = false
				}

			},
		},
		onLoad() {
			this.getList()
		}
	}
</script>

<style lang="scss" scoped>
	.bg {
		background-color: #f7f7f7;
	}

	.no-data {
		width: 100vw;
		height: 100vh;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.address-view {
		height: calc(100vh - 120rpx);
		width: 100vw;
	}

	.footer-nav {
		position: fixed;
		bottom: 0;
		left: 0;
		width: 100vw;
		height: 120rpx;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.pca {
		display: flex;
		justify-content: space-between;
		align-items: center;

		.edit-button {
			margin-right: 24rpx;
			color: #66AAFF;
		}

		.delete-button {
			color: #C80808;
		}
	}
</style>
