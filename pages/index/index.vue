<template>
	<view>
		<view class="container" v-if="!showCropper" :style="{'width': windowWidth +'px','height': windowHeight +'px'}">
			<view style="width: 150rpx;height: 150rpx;border-radius: 50%;
			background-color: #4CD964;border: 1px solid #808080;position: absolute;top: 180rpx;"
			@click.stop.prevent="chooseImg">
				<image v-if="finImgUrl" :src="finImgUrl" style="width: 150rpx;height: 150rpx;border-radius: 50%;" mode="aspectFit"></image>
			</view>
			<text v-if="!finImgUrl" @click.stop.prevent="chooseImg" style="position: absolute;top: 230rpx;">头像</text>
		</view>
		<cpn_cropper v-if="showCropper" :P_imageSrc="imageSrc" :P_imageHeight="imageHeight" :P_imageWidth="imageWidth" :P_rate="rate"
		@sendImage = "showImage"
		></cpn_cropper>
	</view>
</template>

<script>
import cpn_cropper from "../../components/cpn_cropper/cpn_cropper.vue"
export default {
	data() {
		return{
			windowWidth:0,
			windowHeight:0,
			pixelRatio:0,
			imageSrc:"",
			showCropper:false,
			imageWidth:0,
			imageHeight:0,
			rate:0,
			finImgUrl:""
		}
	},
	
	//生命周期函数
	created() {
		uni.getSystemInfo({
			success: res=> {
				//console.log(res)
				this.windowWidth = res.windowWidth;
				this.windowHeight = res.windowHeight;
				this.pixelRatio = res.pixelRatio
			}
		})
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
		},
		
		showImage(finalUrl) {
			this.showCropper = false
			this.finImgUrl = finalUrl
		}
	}
}
</script>

<style scoped>
	.container {display: flex;justify-content: center;align-items: center;}
</style>