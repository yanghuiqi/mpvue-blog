<template>
	<div id="personal-view">
		<div class="personal-item item-userimg">
			<img :src="userInfo.img" alt="" />
			<span>{{userInfo.nickname}}</span>
		</div>
		<div class="personal-item personal-item-col">
			<a href="./../articleList/main?collection=true">
				<div class="personal-col item-collection-note">
					收藏笔记 <span>{{userInfo.collection}}</span>
				</div>
			</a>
			<a href="./../studyList/main">
				<div class="personal-col item-collection-study">资源分享 <span></span></div>
			</a>
		</div>
		<div class="personal-item personal-item-col">
			<a href="">
				<div class="personal-col item-collection-theme" @click="changetheme">设置主题 <span><span :style="{background:userInfo.theme}"></span></span>
				</div>
			</a>
			<a href="./../webInfo/main">
				<div class="personal-col item-collection-authon">关于小程序<span></span></div>
			</a>
		</div>
		<colorpicker :show="colorpick" v-on:cancelpick="changetheme"></colorpicker>
	</div>
</template>

<script>
	import api from '@/common/api'
	import store from '@/common/store'
	import colorpicker from '@/components/colorPicker'
	import {
		mapMutations,
		mapGetters
	} from 'vuex'

	export default {
		components: {
			colorpicker
		},
		data() {
			return {
				colorpick: false
			}
		},
		onLoad() {
//			this.login()
		},
		created() {

		},
		methods: {
			login() {
				let that = this
				wx.getSetting({
					success: function(e) {
						if(e.authSetting['scope.userInfo']) {
							wx.getUserInfo({
								success: function(ress) {
									store.commit("updateUserInfo", {
										img: ress.userInfo.avatarUrl,
										nickname: ress.userInfo.nickName
									})
									wx.login({
										success: function(res) {
											that.$http.post(api.login(), {
												code: res.code,
												encryptedData: ress.encryptedData,
												key: ress.iv
											}).then((res) => {
												if(res.code === 200) {
													wx.showTabBar({
														animation: true
													})
													store.commit("updateId", res.result.data.openid)
													store.commit("updateUserCollection", res.result.data.collection)
													store.commit("changeTheme", res.result.data.theme)
													store.commit("initUserArt", res.result.data.art_collection)
												}
											}).catch((err) => {
												console.log("err", err)
												that.login()
											})
										}
									})
								}
							})
						}
					}
				})
			},
			changetheme() {
				this.colorpick = !this.colorpick
			}
		},
		onHide() {
			this.colorpick = false
		},
		onShow() {
			wx.setNavigationBarColor({
				frontColor: '#ffffff',
				backgroundColor: this.userInfo.theme
			})
		},
		computed: {
			...mapGetters([
				'userInfo',
				'openid',
				'usercollection'
			])
		}
	}
</script>

<style>
	page {
		width: 100%;
		height: 100%;
		background: #EFEFF4;
		position: relative;
	}
	
	#personal-view {
		font-size: 12px;
	}
	
	.personal-item {
		width: 100%;
		border-top: 1px solid #DFDFE1;
		border-bottom: 1px solid #DFDFE1;
		background: #FFF;
		min-height: 40px;
		display: flex;
		align-items: center;
		box-sizing: border-box;
		padding: 0 20px;
		margin-top: 10px;
	}
	
	.personal-item-col {
		flex-direction: column;
		padding: 0;
	}
	
	.personal-item-col>a {
		padding: 0 0 0 20px;
		box-sizing: border-box;
		overflow: hidden;
		display: inline-block;
		width: 100%;
	}
	
	.personal-item-col>a>div {
		width: 100%;
	}
	
	.personal-item:nth-child(1) {
		margin-top: 0;
	}
	
	.item-userimg {
		height: 80px;
	}
	
	.item-userimg>img {
		width: 60px;
		height: 60px;
		border: 1px solid #F2F2F2;
	}
	
	.item-userimg>span {
		margin-left: 16px;
		color: #333;
	}
	
	.personal-col {
		width: 100%;
		height: 100%;
		min-height: 40px;
		box-sizing: border-box;
		padding-left: 16px;
		border-bottom: 1px solid #DFDFE1;
		display: flex;
		align-items: center;
		padding: 0 20px 0 26px;
		position: relative;
	}
	
	.personal-col::before {
		content: "";
		width: 20px;
		height: 20px;
		position: absolute;
		left: 0;
		top: 10px;
		background-size: 100% auto;
		background-repeat: no-repeat;
		background-position: center center;
	}
	
	.personal-item>a:nth-last-child(1)>.personal-col {
		border: none;
	}
	
	.personal-col>span {
		float: right;
		margin-right: 10px;
		position: relative;
		margin-left: auto;
		border-radius: 50%;
		display: inline-block;
		height: 20px;
		line-height: 20px;
		width: 40px;
		text-align: right;
		box-sizing: border-box;
		padding: 0 4px;
		color: #999;
	}
	
	.personal-col>span::before {
		content: "";
		width: 20px;
		height: 20px;
		background-size: 100% auto;
		background-repeat: no-repeat;
		background-position: center center;
		position: absolute;
		left: 44px;
		top: 0;
		background-image: url("data:image/png;base64,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");
	}
	
	.item-collection-note::before {
		width: 16px;
		height: 16px;
		top: 12px;
		background-image: url("data:image/png;base64,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");
	}
	
	.item-collection-theme::before {
		background-image: url("data:image/png;base64,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");
	}
	
	.item-collection-authon::before {
		background-image: url("data:image/png;base64,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");
	}
	
	.item-collection-study::before {
		width: 18px;
		height: 18px;
		top: 11px;
		background-image: url("data:image/png;base64,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");
	}
	
	.item-collection-theme>span span {
		display: inline-block;
		width: 18px;
		height: 18px;
		border: 1px solid #ACACAC;
		background: #5179F1;
	}
</style>