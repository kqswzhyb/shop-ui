<template>
	<view class="login-view">
		<input class="uni-input" type="text" @input="inputUsername" placeholder="输入用户名" />
		<input class="uni-input mb50" password @input="inputPassword" @confirm="login" confirm-type="done" type="text" placeholder="输入密码" />
		<tui-button type="danger" height="66rpx" @tap.native="login">登录</tui-button>
	</view>
</template>

<script>
	import url from "../../../api/index"
	export default {
		data() {
			return {
				username: "",
				password: ""
			};
		},
		methods: {
			inputUsername(e) {
				this.username = e.detail.value
			},
			inputPassword(e) {
				this.password = e.detail.value
			},
			login() {
				if (!this.username) {
					this.tui.toast('请输入用户名', 3000);
					return
				}
				if (!this.password) {
					this.tui.toast('请输入密码', 3000);
					return
				}
				this.tui.request(url.login, "post", {
					userName: this.username,
					password: this.password
				}).then(async res => {
					if (res.data) {
						uni.setStorageSync('token', res.data);
						const result = await this.tui.request(url.userInfo, "get", {
							mode: "user"
						})
						this.$store.commit('setUserInfo', result.data.info)
						this.$store.commit('login')
						this.tui.toast('登录成功', 3000);
						uni.switchTab({
						    url: '../../personal/personal'
						});
					}
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.login-view {
		padding: 40rpx;
	}

	.uni-input {
		margin-bottom: 30rpx;
		padding-bottom: 8rpx;
		border-bottom: 1px solid #f50;
	}
</style>
