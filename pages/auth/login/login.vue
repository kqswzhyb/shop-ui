<template>
	<view class="login-view">
		<input class="uni-input" type="text" @input="inputUsername" placeholder="输入用户名" />
		<input class="uni-input mb50" password @input="inputPassword" @confirm="login" confirm-type="done" type="text"
		 placeholder="输入密码" />
		<tui-button type="danger" height="66rpx" @tap.native="login">登录</tui-button>
		<tui-modal :show="modal" @cancel="init" :custom="true">
			<view class="captcha-view">
				<image :src="bg" ref="bg" mode="widthFix" class="bg-img"></image>
				<slider style="width:100%;margin:0;margin-top:20px;" :value="x" @changing="silderChanging" :min="0" :max="90" @change="sliderChange" />
				<image :src="puzzle" class="puzzle-img" :style="{top: y*imgRate +'px',left:x*imgRate*3.75+'px',width:imgRate*40+'px',height:imgRate*40+'px'}"></image>
			</view>
		</tui-modal>
	</view>
</template>

<script>
	import url from "../../../api/index"
	export default {
		data() {
			return {
				username: "",
				password: "",
				modal: false,
				bg: '',
				puzzle: '',
				uid: '',
				y: 0,
				x: 0,
				width:0
			};
		},
		computed:{
			imgRate(){
				return (this.width*0.84 - 70 )/ 375
			}
		},
		onLoad(){
			let _this=this
			uni.getSystemInfo({
			    success: function (res) {
					_this.width=res.screenWidth
			    }
			});
		},
		methods: {
			init() {
				this.modal = false
			},
			silderChanging(e) {
				this.x=e.detail.value
			},
			sliderChange(){
				this.tui.request(url.login, "post", {
					userName: this.username,
					password: this.password,
					uid:this.uid,
					x:parseInt(this.x *3.75)+''
				}).then(async res => {
					if (res.code==="0") {
						uni.setStorageSync('token', res.data);
						const result = await this.tui.request(url.userInfo, "GET", {
							mode: "user"
						})
						this.$store.commit('setUserInfo', result.data.info)
						this.$store.commit('setAddress', res.data.address)
						this.$store.commit('login')
						this.tui.toast('登录成功', 3000);
						uni.switchTab({
						    url: '../../personal/personal'
						});
					}
					if(res.data==1){
						this.tui.toast('验证失败', 1000);
						this.refreshCaptcha()
					}
				})
			},
			inputUsername(e) {
				this.username = e.detail.value
			},
			inputPassword(e) {
				this.password = e.detail.value
			},
			refreshCaptcha() {
				this.tui.request(url.getCaptcha, "GET").then(res => {
					const data =res.data
					this.bg=data.bg
					this.puzzle=data.puzzle
					this.y=data.y
					this.uid=data.uid
				})
				this.x=0
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
				this.modal = true
				this.refreshCaptcha()
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
	.captcha-view {
		position: relative;
		text-align: center;
		.bg-img {
			width:100%;
		}
		.puzzle-img {
			position: absolute;
		}
	}
</style>
