<script setup>
const { number } = defineProps({
	color: String,
	number: Number,
});

const emits = defineEmits(['checkSequence']);

const checkSequence = () => {
	emits('checkSequence', number);
};
</script>

<template>
	<div
		:class="'game-btn ' + color"
		:data-number="number"
		@click="checkSequence"
	></div>
</template>

<style lang="scss">
.game-btn {
	height: 150px;
	width: 150px;
	opacity: 0.4;
	cursor: pointer;
	position: absolute;
	transition: opacity 0.15s ease;

	&:active {
		opacity: 1;
	}

	&.active {
		opacity: 1;
	}

	&.blue {
		background-color: $blue;
		top: 0;
		left: 0;
	}

	&.red {
		background-color: $red;
		top: 0;
		right: 0;
	}

	&.green {
		background-color: $green;
		bottom: 0;
		right: 0;
	}

	&.yellow {
		background-color: $yellow;
		bottom: 0;
		left: 0;
	}
}
</style>
