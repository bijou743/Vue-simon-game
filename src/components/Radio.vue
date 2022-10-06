<script setup>
import { defineProps, ref, defineEmits, watch } from 'vue';

const emits = defineEmits(['update:modelValue']);

const { modelValue } = defineProps({
	modelValue: Number,
	items: Object,
});

const value = ref(modelValue);

watch(value, () => {
	emits('update:modelValue', value);
});
</script>

<template>
	<div class="radio">
		<div v-for="(item, index) of items" :key="index" class="radio__wrapper">
			<input
				type="radio"
				:value="item.value"
				v-model="value"
				:id="'radio' + index"
			/>
			<label :for="'radio' + index">{{ item.label }}</label>
		</div>
	</div>
</template>

<style lang="scss">
.radio {
	&__wrapper {
		display: flex;
		gap: 4px;
		align-content: center;

		label {
			font-size: 20px;
			cursor: pointer;
		}
	}
}
</style>
