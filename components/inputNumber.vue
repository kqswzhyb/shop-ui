<template>
	<view class="number-picker">
		<view class="number-button" @tap="num+=1">+</view>
		<input class="uni-input" type="number" :value="num" :min="1" />
		<view class="number-button" @tap="subNumber">-</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				num: 1
			}
		},
		methods: {
			subNumber() {
				if (this.num >= 2) {
					this.num -= 1
				}
			},
		},
		watch: {
			num(val) {
				this.$emit("update", this.num)
			}
		}
	}
</script>

<style lang="scss" scoped>
	.number-picker {
		display: flex;

		.number-button {
			padding: 10rpx;
			width: 40rpx;
			height: 40rpx;
			display: flex;
			justify-content: center;
			align-items: center;
			border: 1px solid #333;
			font-size: 18px;
			background-color: #eee;
		}

		.uni-input {
			width: 80rpx;
			text-align: center;
		}
	}
</style>
