<template>
	<view class="container" :style="{'width': windowWidth  +'px','height': windowHeight + 'px'}">
		<image class="img" :src="P_imageSrc" mode="aspectFit" 
		:style="{ 'width': (windowWidth - 20) +'px','height': (windowHeight-50) +'px' }"
		>
		</image>
		<view class="crop_box" :style="{'transform' : 'translate3d(' + transX + 'px,' + transY +'px,'+ transZ + 'px)'}"
		@touchstart.stop.prevent="touchStart" @touchmove.stop.prevent="touchMove">
		</view>
		<canvas canvas-id="myCanvas" :style="{ 'width': (windowWidth - 20) +'px','height': (windowHeight-50) +'px' }"></canvas>
		<button @click.stop.prevent="creatImg" type="warn" style="position: fixed;bottom: 25px;" size="mini">生成头像</button>
	</view>
</template>

<script>
	export default {
		props:{
			P_imageSrc:String,
			P_imageWidth:Number,
			P_imageHeight:Number,
			P_rate:Number
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
				transZ:0,
				pixelRatio:1,
				drawX:0,
				drawY:0,
				left:0,
				top:0,
				tempH:0,
				tempH_01:0
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
			},
			
			creatImg() {
				this.FX_01()
				setTimeout(() =>{
					this.FX_02()
				},500)
				setTimeout(() =>{
					this.FX_03()
				},1000)
			},
			
			FX_01() {
					const query = this.createSelectorQuery()
					query.select(".crop_box").boundingClientRect(data =>{
						this.left = data.left
						this.top = data.top
						//console.log(res)
						console.log(this.left)
						console.log(this.top)
					}).exec()
			},
			
			FX_02() {
				this.tempH = (this.windowWidth - 20)/this.P_rate
				this.tempH_01 = (this.windowHeight - 50 - this.tempH) / 2
				this.drawX = this.left - 10
				this.drawY = this.top - 25 - this.tempH_01
				//console.log(this.P_imageSrc,0,tempH_01,this.windowWidth - 20,tempH)
				console.log("-----------------------------------")
				console.log(this.drawX,this.drawY)
			},
			
			FX_03() {
				let _this = this
				const ctx = uni.createCanvasContext("myCanvas",_this);
				console.log(_this.P_imageSrc,0,_this.tempH_01,_this.windowWidth - 20,_this.tempH)
				ctx.drawImage(_this.P_imageSrc,0,_this.tempH_01,_this.windowWidth - 20,_this.tempH)
				console.log(_this.drawX,_this.drawY)
				ctx.draw()
				uni.canvasToTempFilePath({
					canvasId:"myCanvas",
					x:_this.drawX,
					y:_this.drawY,
					destWidth:200,
					destHeight:200,
					success: (res) => {
						console.log(res)
					},
					fail: (err) => {
						console.log(err)
					}
				})
			}
		}
	}
</script>

<style>
	.container {display:flex;justify-content: center;align-items: center;}
	.img-view {width: 320px;height: 240px;}
	.img {position: absolute;}
	.crop_box {width: 200px;height: 200px;border: 1px solid #2C405A;z-index: 998;position: absolute;left: 0;}
</style>
