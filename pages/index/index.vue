<template>
	<view class="content">
		<view @click="chooseImageA" style="width: 100%; display: flex; flex-direction: row; justify-content: center; background-color: #007AFF;">
			<view :style="'width: '+size+'px; height: '+size+'px;'">
				<canvas canvas-id="shareImg" style="position: absolute; z-index: 999;"></canvas>
				<open-data type="userAvatarUrl"></open-data>
			</view>
		</view>
		<view style="width: 100%; display: flex; justify-content: center; margin-top: 30upx;">
			<button type="primary" @click="save" style="background-color: #007AFF;">保存->相冊</button>
		</view>
		<!-- <view class="text-area">
			<image class="logo" :src="img" style="border: 2px solid #007AFF;"></image>
		</view> -->
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello',
				img: '',
				path: '/static/logo.png',
				ctx: Object,
				size: 150,
			}
		},
		onLoad() {
			uni.authorize({
				scope: 'scope.userInfo', 
				success() {
					uni.getUserInfo({
						success(userInfo) {
							console.log(userInfo)
						},
						fail(userInfo) {
							console.log(userInfo)
						}
					})
				}
			})
			this.draw('/static/logo.png', '/static/logo.png', 150)
		},
		methods: {
			next(){
				let that = this;
				uni.chooseImage({
					success(url) {
						console.log(url.tempFilePaths[0])
						that.draw(that.path, url.tempFilePaths[0], 150)
					}
				})
				
			},
			chooseImageA(){
				let that = this;
				uni.chooseImage({
					success(url) {
						let path = url.tempFilePaths[0];
						that.draw('', url.tempFilePaths[0], 150)
					}
				})
			},
			save(){
				let that = this;
				uni.authorize({
					scope: 'scope.writePhotosAlbum',
					success() {
						uni.saveImageToPhotosAlbum({
							filePath: that.img,
							success() {
								uni.showToast({
									title: '保存成功'
								})
							},
							fail() {
								uni.showToast({
									icon: 'none',
									title: '保存失敗'
								})
							}
						})
					},
					fail() {
						uni.showToast({
							icon: 'none',
							title: '您沒有授權保存到相冊，請重新點擊按鈕'
						})
					}
				})
				
			},
			draw(oldHeadPath, chooseImgPath, size){
				this.size = size;
				let that = this;
				this.ctx = uni.createCanvasContext('shareImg')
				// this.ctx.drawImage(oldHeadPath, 0, 0, size, size)
				this.ctx.drawImage('/static/abc.png', 0, 0, size, size)
				this.ctx.drawImage(chooseImgPath, size-size*0.3, size-size*0.26, size*0.26, size*0.22)
				this.ctx.stroke()
				this.ctx.draw(false, () => {
					uni.canvasToTempFilePath({
					  x: 0,
					  y: 0,
					  width: size,
					  height: size,
					  destWidth: size,
					  destHeight: size,
					  canvasId: 'shareImg',
					  success: function(res) {
					    // 在H5平台下，tempFilePath 为 base64
					    console.log(res.tempFilePath)
						that.img = res.tempFilePath;
					  } 
					})
				});
			}
		}
	}
</script>

<style>
	page{
		height: 100%;
	}
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		background-color: #007AFF;
		height: 100%;
		width: 100%;
		overflow: hidden;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>
