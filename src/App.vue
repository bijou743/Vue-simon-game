<script setup>
import { onMounted, reactive } from 'vue';
import Button from './components/Button.vue';
import Radio from './components/Radio.vue';

const colors = ['blue', 'red', 'green', 'yellow'];
const difficulty = [
	{
		value: 1500,
		label: 'Easy',
	},
	{
		value: 1100,
		label: 'Normal',
	},
	{
		value: 700,
		label: 'Hard',
	},
];

const options = reactive({
	delay: 1100,
	round: 0,
	failed: false,
});

let sequence = [];
let userClicks = 0;
let overlay;
onMounted(() => {
	overlay = document.querySelector('.overlay');
});

function startGame() {
	options.failed = false;
	options.round = 0;

	sequence = [];
	for (let i = 0; i < 2; i++) {
		sequence.push(generateNumber());
	}

	overlay.style.display = 'block';
	setTimeout(() => {
		newRound();
	}, 500);
}

function newRound() {
	userClicks = 0;
	options.round++;
	sequence.push(generateNumber());
	highlightSequence(options.delay);
}

async function highlightSequence(delay) {
	highlight(0);

	await new Promise((resolve) => {
		let i = 1;
		const interval = setInterval(() => {
			highlight(i);

			i++;
			if (i === sequence.length) {
				clearInterval(interval);
				resolve();
			}
		}, delay);
	});

	await new Promise(() =>
		setTimeout(() => {
			overlay.style.display = 'none';
		}, 500)
	);
}

function highlight(number) {
	playSound(sequence[number]);
	const button = document.querySelector(
		`.game-btn.${colors[sequence[number]]}`
	);
	button.classList.add('active');

	setTimeout(() => {
		button.classList.remove('active');
	}, 300);
}

function generateNumber() {
	return Math.floor(Math.random() * colors.length);
}

function playSound(number) {
	const audio = document.querySelector(`.audio-${number}`);
	audio.play();
}

function checkSequence(number) {
	playSound(number);

	if (sequence[userClicks] === number) {
		userClicks++;

		if (userClicks == sequence.length) {
			overlay.style.display = 'block';
			setTimeout(() => {
				newRound();
			}, 800);
		}
	} else {
		options.failed = true;
		overlay.style.display = 'block';
	}
}
</script>

<template>
	<header>
		<h1>Simon Game</h1>

		<p>Press "Start new game" and repeat sequence by clicking the buttons.</p>
	</header>

	<main>
		<div class="game-buttons">
			<Button color="blue" :number="0" @checkSequence="checkSequence" />
			<Button color="red" :number="1" @checkSequence="checkSequence" />
			<Button color="green" :number="2" @checkSequence="checkSequence" />
			<Button color="yellow" :number="3" @checkSequence="checkSequence" />
			<div class="overlay"></div>
		</div>

		<div class="settings">
			<div class="round">
				<h2>Round: {{ options.round }}</h2>
			</div>

			<div v-if="options.failed" class="loser">
				You lose after {{ options.round }} rounds!
			</div>

			<div class="difficulty">
				<h2>Difficulty:</h2>
				<Radio :items="difficulty" v-model="options.delay" />
			</div>

			<button class="btn" @click="startGame">Start new game</button>
		</div>
	</main>

	<div class="sounds">
		<audio class="audio-0">
			<source src="./assets/sounds/sounds_1.mp3" />
		</audio>
		<audio class="audio-1">
			<source src="./assets/sounds/sounds_2.mp3" />
		</audio>
		<audio class="audio-2">
			<source src="./assets/sounds/sounds_3.mp3" />
		</audio>
		<audio class="audio-3">
			<source src="./assets/sounds/sounds_4.mp3" />
		</audio>
	</div>
</template>

<style lang="scss">
header {
	text-align: center;

	p {
		font-size: 20px;
	}
}

main {
	display: flex;
	gap: 40px;
	justify-content: center;
	margin-top: 25px;
}

.game-buttons {
	width: 300px;
	height: 300px;
	background-color: #fff;
	box-shadow: 0 0 4px 2px rgba(0, 0, 0, 0.1);
	position: relative;
	overflow: hidden;
	border-radius: 10px;

	.overlay {
		width: 300px;
		height: 300px;
		position: absolute;
		z-index: 1;
	}
}

.settings {
	flex-basis: 260px;
}

.round {
	margin-bottom: 10px;
}

.loser {
	color: $red;
	font-size: 1.5em;
	font-weight: bold;
	margin-bottom: 10px;
}

@media (max-width: 660px) {
	main {
		flex-direction: column;
	}
}
</style>
