<template>
	<div style="display: none">
		<canvas id="canvas-official-seal" ref="canvasOfficialSeal"></canvas>
	</div>
</template>

<script lang="ts" setup>
import { ref } from 'vue'
const props = defineProps(['companyName', 'text'])
const canvasOfficialSeal = ref()

const initCanvas = () => {
	const canvas = canvasOfficialSeal.value
	const context = canvas.getContext('2d')
	// 绘制印章边框
	const width = canvas.width / 2
	const height = canvas.height / 2
	canvasOfficialSeal.value.width = 160
	canvasOfficialSeal.value.height = 160
	context.lineWidth = 3 // 椭圆宽度
	context.strokeStyle = '#f00'
	context.textBaseline = 'middle' // 设置文本的垂直对齐方式
	context.textAlign = 'center' // 设置文本的水平对对齐方式
	// 3个参数： 左边距 上边据 宽度 椭圆扁度
	BezierEllipse4(context, width, height, 79, 55) // 椭圆
	// 画五角星
	// create5star(context, width, height, 15, '#f00', 0)
	// 绘制印章名称
	createSealName(context, width, height)
	// 绘制印章单位
	createCompanyName(context, width, height)
	return canvas.toDataURL()
}
// 绘制印章名称
const createSealName = (context: CanvasRenderingContext2D, width: number, height: number) => {
	context.font = 'bolder 15px 宋体'
	context.textBaseline = 'middle' // 设置文本的垂直对齐方式
	context.textAlign = 'center' // 设置文本的水平对对齐方式
	context.lineWidth = 1
	context.fillStyle = '#f00'
	context.fillText(props.text, width, height + 35)
	context.save()
}
// 绘制印章单位
const createCompanyName = (context: CanvasRenderingContext2D, width: number, height: number) => {
	const ccircle = {
		x: width,
		y: height,
		radius: 58,
	}
	let cstartAngle = 170 // 控制字符起始位置度数
	const cendAngle = 15 // 首位字符相隔度数
	const cradius = ccircle.radius // 圆的半径
	const cangleDecrement = (cstartAngle - cendAngle) / (props.companyName.length - 1.3) // 每个字母占的弧度
	context.font = '15px 宋体'
	const cratioX = 66 / ccircle.radius // 横轴缩放比率
	const cratioY = 57 / ccircle.radius // 纵轴缩放比率
	// 进行缩放（均匀压缩）
	context.scale(cratioX, cratioY)
	for (let cindex = 0; cindex < props.companyName.length; cindex++) {
		context.save()
		context.beginPath()
		// 绘制点
		context.translate(ccircle.x + Math.cos((Math.PI / 180) * cstartAngle) * cradius - 10, ccircle.y - Math.sin((Math.PI / 180) * cstartAngle) * cradius + 14)
		context.rotate(Math.PI / 2 - (Math.PI / 180) * cstartAngle) // Math.PI/2为旋转90度  Math.PI/180*X为旋转多少度
		context.fillText(props.companyName.charAt(cindex), 0, 0)
		context.strokeText(props.companyName.charAt(cindex), 0, 0)
		cstartAngle = cstartAngle - cangleDecrement
		context.restore()
	}
}
defineExpose({ initCanvas })
// 绘制五角星
// const create5star = (
//     context: CanvasRenderingContext2D,
//     sx: number,
//     sy: number,
//     radius: number,
//     color: string,
//     rotato: number
// ) => {
//     context.save()
//     context.fillStyle = color
//     context.translate(sx, sy) // 移动坐标原点
//     context.rotate(Math.PI + rotato) // 旋转
//     context.beginPath() // 创建路径
//     const dig = (Math.PI / 5) * 4
//     for (let i = 0; i < 5; i++) {
//         // 画五角星的五条边
//         const x = Math.sin(i * dig)
//         const y = Math.cos(i * dig)
//         context.lineTo(x * radius, y * radius)
//     }
//     context.closePath()
//     context.stroke()
//     context.fill()
//     context.restore()
// }
const BezierEllipse4 = (ctx: CanvasRenderingContext2D, x: number, y: number, a: number, b: number) => {
	const k = 0.556
	const ox = a * k // 水平控制点偏移量
	const oy = b * k // 垂直控制点偏移量</p> <p>
	ctx.beginPath()
	// 从椭圆的左端点开始顺时针绘制四条三次贝塞尔曲线
	ctx.moveTo(x - a, y)
	ctx.bezierCurveTo(x - a, y - oy, x - ox, y - b, x, y - b)
	ctx.bezierCurveTo(x + ox, y - b, x + a, y - oy, x + a, y)
	ctx.bezierCurveTo(x + a, y + oy, x + ox, y + b, x, y + b)
	ctx.bezierCurveTo(x - ox, y + b, x - a, y + oy, x - a, y)
	ctx.closePath()
	ctx.stroke()
}
</script>
