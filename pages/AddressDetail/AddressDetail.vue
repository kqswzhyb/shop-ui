<template>
	<view>
		<form @submit="formSubmit" @reset="formReset">
			<tui-list-cell :hover="false">
				<view class="tui-line-cell">
					<view class="tui-title">收货人</view>
					<input placeholder-class="tui-phcolor" :value="form.contactName" @input="e=>form.contactName=e.detail.value" class="tui-input"
					 name="contactName" placeholder="请输入姓名" maxlength="50" type="text" />
				</view>
			</tui-list-cell>
			<tui-list-cell :hover="false">
				<view class="tui-line-cell">
					<view class="tui-title">手机号</view>
					<input placeholder-class="tui-phcolor" :value="form.contactPhone" class="tui-input" @input="e=>form.contactPhone=e.detail.value"
					 name="contactPhone" placeholder="请输入手机号" maxlength="50" type="text" />
				</view>
			</tui-list-cell>
			<tui-list-cell :hover="false" @tap.native="showPicker">
				<view class="tui-line-cell">
					<view class="tui-title">地区</view>
					<p style="width:100%;height: 100%;">{{`${form.provinceName} ${form.cityName} ${form.areaName}`}}</p>
					<input placeholder-class="tui-phcolor" :value="form.provinceName" class="tui-input" name="provinceId" placeholder="请输入姓名"
					 maxlength="50" type="hidden" />
				</view>
			</tui-list-cell>
			<tui-list-cell :hover="false">
				<view class="tui-line-cell">
					<view class="tui-title">详情地址</view>
					<input placeholder-class="tui-phcolor" :value="form.addressDetail" class="tui-input" @input="e=>form.addressDetail=e.detail.value"
					 name="addressDetail" placeholder="请输入详情地址" maxlength="50" type="text" />
				</view>
			</tui-list-cell>
			<tui-list-cell :hover="false">
				<view class="tui-line-cell">
					<view class="tui-title">备注</view>
					<input placeholder-class="tui-phcolor" :value="form.remark" class="tui-input" @input="e=>form.remark=e.detail.value"
					 name="remark" placeholder="请输入备注" maxlength="50" type="text" />
				</view>
			</tui-list-cell>

			<view class="tui-btn-box">
				<button class="tui-button-primary" hover-class="tui-button-hover" formType="submit" type="primary">提交</button>
				<button class="tui-button-primary tui-button-gray" hover-class="tui-button-gray_hover" type="warn" formType="reset">重置</button>
			</view>
		</form>
		<view class="tui-mask-screen" :class="[showPickerStatus?'tui-mask-show':'']" @tap="hidePicker"></view>
		<view class="tui-picker-box" :class="[showPickerStatus?'tui-pickerbox-show':'']">
			<view class="picker-header tui-list-item">
				<view class="btn-cancle" hover-class="tui-opcity" :hover-stay-time="150" @tap.stop="hidePicker">取消</view>
				<view class="btn-sure" hover-class="tui-opcity" :hover-stay-time="150" @tap.stop="picker">确定</view>
			</view>
			<picker-view indicator-style="height: 50px;" class="picker-view" :value="value" @change="columnPicker">
				<picker-view-column>
					<view v-for="(item,index) in province" :key="item.provinceid" class="item">{{item.province}}</view>
				</picker-view-column>
				<picker-view-column>
					<view v-for="(item,index) in city" :key="item.cityid" class="item">{{item.city}}</view>
				</picker-view-column>
				<picker-view-column>
					<view v-for="(item,index) in area" :key="item.areaid" class="item">{{item.area}}</view>
				</picker-view-column>
			</picker-view>
		</view>
	</view>
</template>

<script>
	import url from '../../api/index'
	const form = require("../../ThorUI-components/common/tui-validation/tui-validation.js")
	export default {
		data() {
			return {
				showPickerStatus: false,
				form: {
					contactName: '',
					contactPhone: '',
					provinceId: '',
					provinceName: "",
					cityId: '',
					cityName: '',
					areaId: '',
					areaName: '',
					addressId: '',
					addressDetail: '',
					remark: ''
				},
				value: [0, 0, 0],
				city: [],
				province: [],
				area: [],
				mode: ''
			};
		},
		methods: {
			picker() {
				const province = this.province[this.value[0]]
				const city = this.city[this.value[1]]
				const area = this.area[this.value[2]]
				this.form.provinceId = province.provinceId
				this.form.provinceName = province.province
				this.form.cityId = city.cityId
				this.form.cityName = city.city
				this.form.areaId = area.areaId
				this.form.areaName = area.area
				this.hidePicker()
			},
			columnPicker(e) {
				let value = e.detail.value;
				//如果两者下标不一致，表示滚动过
				if (this.province[this.value[0]].provinceId !== this.province[value[0]].provinceId) {
					this.getCity(this.province[value[0]].provinceId)
					this.value[0] = value[0]
				} else if (this.city[this.value[1]].cityId !== this.city[value[1]].cityId) {
					this.value[0] = value[0]
					this.value[1] = value[1]
					this.getArea(this.city[value[1]].cityId)
				} else {
					this.value[2] = value[2]
				}
			},
			showPicker: function() {
				this.showPickerStatus = true
			},
			// 隐藏picker-view
			hidePicker: function() {
				this.showPickerStatus = false
			},
			init() {
				this.form = {
					contactName: '',
					contactPhone: '',
					provinceId: '',
					provinceName: "",
					cityId: '',
					cityName: '',
					areaId: '',
					areaName: '',
					addressId: '',
					addressDetail: '',
					remark: ''
				}
			},
			formSubmit: function(e) {
				//表单规则
				let rules = [{
					name: "contactName",
					rule: ["required", "isChinese", "minLength:2", "maxLength:6"], //可使用区间，此处主要测试功能
					msg: ["请输入姓名", "姓名必须全部为中文", "姓名必须2个或以上字符", "姓名不能超过6个字符"]
				}, {
					name: "contactPhone",
					rule: ["required", "isMobile"],
					msg: ["请输入手机号", "请输入正确的手机号"]
				}, {
					name: "provinceId",
					rule: ["required"],
					msg: ["请选择省市区"]
				}, {
					name: "addressDetail",
					rule: ["required"],
					msg: ["请输入详情地址"]
				}, ];
				//进行表单检查
				let checkRes = form.validation(e.detail.value, rules);
				if (!checkRes) {
					this.tui.request(this.mode === 'add' ? url.addAddress : url.updateAddress, this.mode === 'add' ? "POST" : "PUT",
						this.form).then(res => {
						uni.showToast({
							title: "提交成功",
							icon: "success"
						});
						uni.navigateBack({
							delta: 1
						})
					})
				} else {
					uni.showToast({
						title: checkRes,
						icon: "none"
					});
				}
			},
			formReset: function(e) {
				this.init()
			},
			async getCity(id) {
				this.tui.request(url.cityList + id, 'GET').then(res => {
					this.city = res.data
					this.value[1] = 0
					this.tui.request(url.areaList + this.city[0].cityId, 'GET').then(area => {
						this.area = area.data
						this.value[2] = 0
					})
				})
			},
			async getArea(id) {
				this.tui.request(url.areaList + id, 'GET').then(res => {
					this.area = res.data
					this.value[2] = 0
				})
			}
		},
		async onLoad(option) {
			this.mode = option.mode
			this.init()
			this.value = [0, 0, 0]
			if (option.mode === 'add') {
				const province = await this.tui.request(url.provinceList + '001', 'GET')
				this.province = province.data
				const city = await this.tui.request(url.cityList + this.province[0].provinceId, 'GET')
				this.city = city.data
				const area = await this.tui.request(url.areaList + this.city[0].cityId, 'GET')
				this.area = area.data


				this.init()
			} else {
				this.tui.request(url.getAddressById + option.id, "GET").then(async res => {
					this.form = res.data
					const province = await this.tui.request(url.provinceList + '001', 'GET')
					this.province = province.data
					const city = await this.tui.request(url.cityList + this.form.provinceId, 'GET')
					this.city = city.data
					const area = await this.tui.request(url.areaList + this.form.cityId, 'GET')
					this.area = area.data
					this.value = [this.province.findIndex(v => v.provinceId === this.form.provinceId), this.city.findIndex(
						v => v.cityId === this.form.cityId), this.area.findIndex(v => v.areaId === this.form.areaId)]
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.container {
		padding: 20rpx 0 50rpx 0;
	}

	.tui-line-cell {
		width: 100%;
		box-sizing: border-box;
		display: flex;
		align-items: center;
	}

	.tui-title {
		line-height: 32rpx;
		min-width: 120rpx;
		flex-shrink: 0;
	}

	.tui-input {
		font-size: 32rpx;
		color: #333;
		padding-left: 20rpx;
		flex: 1;
		overflow: visible;
	}

	.radio-group {
		margin-left: auto;
		transform: scale(0.8);
		transform-origin: 100% center;
		flex-shrink: 0;
	}

	.tui-radio {
		display: inline-block;
		padding-left: 28rpx;
		font-size: 36rpx;
		vertical-align: middle;
	}


	.tui-btn-box {
		padding: 40rpx 50rpx;
		box-sizing: border-box;
	}

	.tui-button-gray {
		margin-top: 30rpx;
	}

	.tui-mask-screen {
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background: rgba(0, 0, 0, 0.6);
		z-index: 99996;
		transition: all 0.3s ease-in-out;
		opacity: 0;
		visibility: hidden;
	}

	.tui-mask-show {
		opacity: 1;
		visibility: visible;
	}

	.tui-picker-box {
		width: 100%;
		position: fixed;
		left: 0;
		right: 0;
		bottom: 0;
		z-index: 99999;
		visibility: hidden;
		transform: translate3d(0, 100%, 0);
		transform-origin: center;
		transition: all 0.3s ease-in-out;
		min-height: 20rpx;
		background: #fff;
	}

	.tui-pickerbox-show {
		transform: translate3d(0, 0, 0);
		visibility: visible;
	}

	.picker-header {
		width: 100%;
		height: 90rpx;
		padding: 0 46rpx;
		display: flex;
		justify-content: space-between;
		align-items: center;
		box-sizing: border-box;
		font-size: 32rpx;
		background: #fff;
	}

	.tui-list-item::after {
		left: 0;
	}

	.btn-cancle {
		padding: 20rpx;
		color: #888;
	}

	.btn-sure {
		padding: 20rpx;
		color: #5677fc;
	}

	.picker-view {
		width: 100%;
		height: 260px;
	}

	.item {
		line-height: 50px;
		text-align: center;
	}
</style>
