<template>
	<div>
		<input placeholder="公司名称" v-model="form.companyName" @blur="blur" />
		<input placeholder="字体环绕" v-model="form.text" />
	</div>
	<img :src="form.src" alt="" />
	<Seal v-bind="form" ref="seal"></Seal>
	<Selected
		:list="checked"
		@selected="selected"
		:config="{
			checked: 'checked',
			key: 'id',
			label: 'title',
		}"
	></Selected>
</template>

<script setup lang="ts">
import { reactive, ref } from 'vue'
import Seal from '@/components/seal.vue'
import Selected from '@/components/selected.vue'
const form = reactive({
	companyName: '',
	text: '',
	src: '',
})
interface CheckBox {
	id: number
	title: string
	checked: boolean
}
const checked = ref<CheckBox[]>([])
for (let i = 0; i < 100; i++) {
	checked.value.push({
		id: i + 1,
		title: `content${i + 1}`,
		checked: false,
	})
}
const selected = (list: unknown[]) => {
	console.log(list)
}
const seal = ref()
const blur = () => {
	if (form.companyName && form.text) {
		form.src = seal.value.initCanvas()
	}
}
</script>

<style>
#app {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
	margin-top: 60px;
}
</style>
