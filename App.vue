<script>
	import url from './api/index.js'
	export default {
		onLaunch: function() {
			this.tui.request(url.userInfo, "get", {
				mode: "user"
			}).then(res=>{
				this.$store.commit('setUserInfo', res.data.info)
			})
			// uni.getLocation({
			// 	type: 'wgs84',
			// 	success(res) {
			// 		uni.request({
			// 			url: `https://apis.map.qq.com/ws/geocoder/v1/?location=${res.latitude},${res.longitude}&key=xxxx`,
			// 			success(result) {
			// 				this.$store.commit('setLocation', result.data.result.ad_info)
			// 			}
			// 		})
			// 	}
			// });
			
			
		},
		onLoad: function() {
			console.log('load')

		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	@import url("./uni.scss");

	* {
		padding: 0;
		margin: 0;
		box-sizing: border-box;
	}

	/*每个页面公共css */
</style>
