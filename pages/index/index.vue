<template>
	<view>
		<view v-if="!showCropper" style="display: flex;justify-content: center;align-items: center;width: 375px;height: 500px;">
			<view style="width: 150rpx;height: 150rpx;border-radius: 50%;
			background-color: #4CD964;border: 1px solid #808080;position: absolute;top: 180rpx;"
			@click.stop.prevent="chooseImg">
			</view>
			<text @click.stop.prevent="chooseImg" style="position: absolute;top: 230rpx;">头像</text>
		</view>
		<cpn_cropper v-if="showCropper" :P_imageSrc="imageSrc" :P_imageHeight="imageHeight" :P_imageWidth="imageWidth" :P_rate="rate"></cpn_cropper>
	</view>
</template>

<script>
import cpn_cropper from "../../components/cpn_cropper/cpn_cropper.vue"
export default {
	data() {
		return{
			imageSrc:"",
			showCropper:false,
			imageWidth:0,
			imageHeight:0,
			rate:0
		}
	},
	
	components:{
		cpn_cropper
	},
	
	methods:{
		chooseImg() {
			let that = this
			uni.chooseImage({
				count: 1,
				sizeType: ['compressed'],
				sourceType: ['album'],
				success: res=> {
					this.imageSrc = res.tempFilePaths[0]
					uni.getImageInfo({
						src: res.tempFilePaths[0],
						success: function (image) {
							that.imageWidth = image.width
							that.imageHeight = image.height
							that.rate = image.width / image.height
							that.showCropper = true
						}
					})
				}
			})
		}
	}
}
</script>

<style scoped>
	
</style>