<template>
	<view class="no-data" v-if="!isLogin">
		<text class="fs24">{{text}}</text>
	</view>
</template>

<script>
	import {
		mapGetters
	} from 'vuex'
	export default {
		props: {
			text: {
				type: String,
				default: '请先登录'
			}

		},
		data() {
			return {

			}
		},
		computed: {
			...mapGetters(["isLogin"])
		}
	}
</script>

<style lang="scss" scoped>
	.no-data {
		width: 100%;
		height: 100%;
		display: flex;
		justify-content: center;
		align-items: center;
	}
</style>
