<template>
	<view class="number-picker">
		<view class="number-button" @tap="subNumber">-</view>
		<input class="uni-input" type="number" :value="num" :min="0" :max="max" />
		<view class="number-button" @tap="addNumber">+</view>
	</view>
</template>

<script>
	export default {
		props: {
			max: {
				type: Number,
				default: 0
			},
			number: {
				type: Number,
				default: 0
			}
		},
		data() {
			return {
				num: 0
			}
		},
		methods: {
			subNumber() {
				if (this.num >= 2) {
					this.num -= 1
				}
			},
			addNumber() {
				if (this.num <= this.max - 1) {
					this.num += 1
				}
			},
			setNum(val) {
				this.num = val
			}
		},
		watch: {
			num(val) {
				this.$emit("update", this.num)
			},
			number(val) {
				this.num = val
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
