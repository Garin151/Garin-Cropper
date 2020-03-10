<template>
	<view>
		<view v-if="isShow" class="container" :style="{'width': windowWidth  +'px','height': windowHeight + 'px'}">
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
		<view v-if="!isShow" class="container" :style="{'width': windowWidth  +'px','height': windowHeight + 'px'}">
			<canvas canvas-id="Fordraw" :style="{ 'width': (windowWidth - 20) +'px','height': (windowHeight-50) +'px' }"></canvas>
		</view>
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
				windowWidth:0,//可用的屏幕宽度
				windowHeight:0,//可用的屏幕高度
				startX:0,//手指开始点击的X
				startY:0,//手指开始点击的Y
				offsetX:0,//重新点击时，需要减去的之前移动的距离，等于transX
				offsetY:0,//重新点击时，需要减去的之前移动的距离，等于transY
				transX:0,//裁剪框移动的X
				transY:0,//裁剪框移动的Y
				transZ:0,//裁剪框移动的Z，没什么用
				pixelRatio:1,//手机缩放像素比，后面生成图片，乘以这个值，生成 1:1图片
				drawX:0,//指定画布生成图片的X
				drawY:0,//指定画布生成图片的Y
				left:0,
				top:0,
				tempH:0,
				tempH_01:0,
				isShow:true
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
			
			async creatImg() {
				try{
					this.isShow = false
					let a = await this.FX_01()
					console.log(a)
					let b = await this.FX_02()
					console.log(b)
					let c = await this.FX_03()
					console.log(c)
				}catch(err) {
					console.log(err)
				}
			},
			
			FX_01() {
				return new Promise((resolve,reject) =>{
					//在组件，或包含组件的页面里，应使用this.createSelectorQuery()
					const query = this.createSelectorQuery()
					query.select(".crop_box").boundingClientRect(data =>{
						this.left = data.left
						this.top = data.top
						resolve("left: " + this.left + "top: " + this.top)
					}).exec()
				})
			},
			
			FX_02() {
				return new Promise((resolve,reject) =>{
					this.tempH = (this.windowWidth - 20)/this.P_rate
					this.tempH_01 = (this.windowHeight - 50 - this.tempH) / 2
					this.drawX = this.left - 10
					this.drawY = this.top - 25 - this.tempH_01
					resolve("drawX: " + this.drawX + "drawY: " + this.drawY)
				})
			},
			
			FX_03() {
				return new Promise((resolve,reject) =>{
					const ctx = uni.createCanvasContext("Fordraw",this);
					ctx.drawImage(this.P_imageSrc,0,this.tempH_01,this.windowWidth - 20,this.tempH)
					//避免异步，防止因画布还没画图像，就开始执行导出画布图片的方法。所以将uni.canvasToTempFilePath()放入draw()完成后的回调函数
					ctx.draw(false,() =>{
						uni.canvasToTempFilePath({
							canvasId:"Fordraw",
							x:this.drawX + 2,
							y:this.drawY + 15,
							width:200,
							height:200,
							fileType:"jpg",
							quality:1,
							// destWidth:
							// destHeight:
							success: (res) => {
								let finalUrl = res.tempFilePath
								this.$emit("sendImage",finalUrl)
								resolve(finalUrl)
							},
							fail: (err) => {
								console.log(err)
							}
						},this)
					})
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
