<template>
	<view class="no-data" v-if="!isLogin">
		<text class="fs24">请先登录</text>
	</view>
</template>

<script>
	import {mapGetters} from 'vuex'
	export default {
		data() {
			return {
				
			}
		},
		computed:{
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
