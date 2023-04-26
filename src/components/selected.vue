<!-- 拖动选中demo -->
<template>
	<div class="checkbox" @mousedown="onMouseDown($event)" ref="parent" @mouseup="mouseup">
		<div v-for="item in list" ref="item" :key="item[config.key]" :class="['checkbox-item', item[config.checked] ? 'selected-style' : '']" @click="select(item)">
			{{ item[config.label] }}
		</div>
		<div ref="mask" class="mask"></div>
	</div>
</template>
<script lang="ts" setup>
import { ref, onMounted } from 'vue'
type ItemObj = { [key: string]: boolean }
const props = defineProps(['list', 'config'])
const emit = defineEmits<{ (e: 'selected', list: ItemObj[]): void }>()

const select = (item: ItemObj) => {
	item[props.config.checked] = !item[props.config.checked]
}
const parent = ref()
const item = ref()
const mask = ref()
// 鼠标左键点击触发
const onMouseDown = (e: MouseEvent) => {
	if (e.button !== 0) return
	parent.value.addEventListener('mousemove', getMousePosition)
	/**
	 * offsetLeft  不满足条件
	 * 因为该值来源是 就近父元素的定位
	 * 所以遮罩层起始位置会发生偏移
	 * getBoundingClientRect
	 * 这个方法完美的解决的此问题, 其中获取该元素距离容器所有坐标值
	 * */
	const rect = parent.value.getBoundingClientRect()
	startTop = e.y - Math.round(rect.top)
	startLeft = e.x - Math.round(rect.left)
	mask.value.style.opacity = 0.4
}
const mouseup = () => {
	resetPosition()
}
// 初始化标签事件
const initEmit = () => {
	parent.value.addEventListener('mouseleave', () => {
		resetPosition()
	})
}
onMounted(() => {
	initEmit()
})
// 重置依赖数据
const resetPosition = () => {
	selected.value.forEach(v => {
		select(v)
	})
	if (selected.value.length) emit('selected', selected.value)
	parent.value.removeEventListener('mousemove', getMousePosition)
	mask.value.style.width = 0
	mask.value.style.height = 0
	startTop = 0
	endTop = 0
	startLeft = 0
	endLeft = 0
	// 清空选中
	selected.value = []
}
// 选中的元素
let selected = ref<ItemObj[]>([])
let startTop = 0,
	endTop = 0,
	startLeft = 0,
	endLeft = 0
const getMousePosition = () => {
	// 获取遮罩层在 元素中的位置
	const maskPosition = mask.value.getBoundingClientRect()
	selected.value = []
	for (let i = 0; i < item.value.length; i++) {
		const { left, top, right, bottom } = item.value[i].getBoundingClientRect()
		// 鼠标拖动区域相关高度处理   遮罩层覆盖区域包含状态下被push进数组
		if (right > maskPosition.left && bottom > maskPosition.top && left < maskPosition.right && top < maskPosition.bottom) {
			selected.value.push(props.list[i])
		}
	}
	const rect = parent.value.getBoundingClientRect()
	// 获取最后位置
	endTop = (window.event as MouseEvent).y - Math.round(rect.top)
	endLeft = (window.event as MouseEvent).x - Math.round(rect.left)

	// 设置遮罩层定位样式
	mask.value.style.top = Math.min(startTop, endTop) + 'px'
	mask.value.style.left = Math.min(startLeft, endLeft) + 'px'

	// 鼠标拖动  定义遮罩层宽高
	mask.value.style.width = Math.abs(startLeft - endLeft) + 'px'
	mask.value.style.height = Math.abs(startTop - endTop) + 'px'
}
</script>
<style>
.checkbox {
	width: 100%;
	display: flex;
	flex-wrap: wrap;
	user-select: none;
	position: relative;
	cursor: default;
}

.checkbox-item {
	width: 100px;
	height: 40px;
	text-align: center;
	line-height: 40px;
	border: 1px solid #ccc;
	cursor: pointer;
	margin-right: -1px;
	margin-bottom: -1px;
}
.selected-style {
	color: white;
	background-color: #1890ff;
}
.checkbox .mask {
	position: absolute;
	top: 0;
	left: 0;
	width: 0;
	height: 0;
	background-color: #1890ff;
	opacity: 0;
}
</style>
