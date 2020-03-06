<template>
	<view class="container" :style="{'width': windowWidth  +'px','height': windowHeight + 'px'}">
		<image class="img" :src="P_imageSrc" mode="aspectFit" 
		:style="{ 'width': (windowWidth - 20) +'px','height': (windowHeight-50) +'px' }"
		>
		</image>
		<view class="crop_box" :style="{'transform' : 'translate3d(' + transX + 'px,' + transY +'px,'+ transZ + 'px)'}"
		@touchstart.stop.prevent="touchStart" @touchmove.stop.prevent="touchMove">
		</view>
	</view>
</template>

<script>
	export default {
		props:{
			P_imageSrc:String
		},
		
		data() {
			return {
				windowWidth:0,
				windowHeight:0,
				startX:0,
				startY:0,
				offsetX:0,
				offsetY:0,
				transX:0,
				transY:0,
				transZ:0
			}
		},
		
		//生命周期函数
		created() {
			uni.getSystemInfo({
				success: res=> {
					//console.log(res)
					this.windowWidth = res.windowWidth;
					this.windowHeight = res.windowHeight;
				}
			})
		},
		
		methods: {
			touchStart(e) {
				this.startX = e.changedTouches[0].clientX - this.offsetX;
				this.startY = e.changedTouches[0].clientY - this.offsetY;
			},
			
			touchMove(e) {
				let moveX = e.changedTouches[0].clientX;
				let moveY = e.changedTouches[0].clientY;
				this.transX = moveX - this.startX;
				this.transY = moveY - this.startY;
				this.offsetX = this.transX;
				this.offsetY = this.transY;
			}
		}
	}
</script>

<style>
	.container {display:flex;justify-content: center;align-items: center;}
	.img-view {width: 320px;height: 240px;}
	.img {position: absolute;}
	.crop_box {width: 200px;height: 200px;border: 1px solid #2C405A;z-index: 998;position: absolute;}
</style>
