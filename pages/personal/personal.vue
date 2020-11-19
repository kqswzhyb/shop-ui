<template>
	<view>
		{{isLogin?'个人中心':'请先登录'}}
	</view>
</template>

<script>
	import {
		mapGetters
	} from 'vuex'
	export default {
		data() {
			return {

			};
		},
		computed: {
			...mapGetters(['isLogin'])
		},
		onLoad() {}
	}
</script>

<style lang="scss">

</style>
