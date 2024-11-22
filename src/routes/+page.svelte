<script lang="ts">
	import Logo from './Logo.svelte';
	import { onMount } from 'svelte';

	const initialText: string = 'qwertyuiopasdfghjklzxcvbnm';

	type letter = {
		letter: string;
		typed: string;
	};

	let text: letter[] = $state([]);
	let currentLetterIndex: number = $state(0);

	let time = $state({
		cs: 0,
		s: 0,
		m: 0
	});
	let timerInterval: number | null = $state(null);

	let finishedGame: boolean = $state(false);
	const initGame = () => {
		finishedGame = false;
		if (timerInterval) clearInterval(timerInterval);
		timerInterval = null;
		time = {
			cs: 0,
			s: 0,
			m: 0
		};
		text = [];
		[...initialText].forEach((letter) => {
			text.push({
				letter: letter,
				typed: 'not'
			});
		});
		currentLetterIndex = 0;
	};

	const handleKeyDown = ({ key }: KeyboardEvent) => {
		if (key !== 'Backspace' && key.length !== 1) {
			if (key === 'Enter') {
				return initGame();
			} else {
				return;
			}
		}
		if (!timerInterval && !finishedGame) {
			timerInterval = setInterval(() => {
				time.cs++;
				if (time.cs === 100) {
					time.s++;
					time.cs = 0;
					if (time.s === 60) {
						time.m++;
						time.m = 0;
					}
				}
			}, 10);
		}
		text.map((l, i) => {
			if (i === currentLetterIndex) {
				if (l.letter === key) {
					l.typed = 'correct';
				} else if (key !== 'Backspace') {
					l.typed = 'incorrect';
				}
			} else if (i === currentLetterIndex - 1 && key === 'Backspace') {
				l.typed = 'not';
			}
			return l;
		});
		if (key === 'Backspace') {
			if (currentLetterIndex !== 0) {
				currentLetterIndex--;
			}
		} else if (currentLetterIndex !== text.length) {
			currentLetterIndex++;
			if (
				currentLetterIndex === text.length &&
				!text.find((letter) => letter.typed === 'incorrect')
			) {
				finishedGame = true;
				if (timerInterval) {
					clearInterval(timerInterval);
				}
				timerInterval = null;
			}
		}
	};

	onMount(() => {
		initGame();

		document.addEventListener('keydown', handleKeyDown);
	});
</script>

<div id="container" class="flex h-screen select-none flex-col">
	<div id="nav" class="flex h-16 w-full items-center p-2">
		<div id="nav-logo" class="cursor-pointer">
			<Logo />
		</div>
	</div>

	<div id="main" class="flex w-full flex-1 items-center justify-center p-4">
		<div class="flex w-3/5 flex-col items-center justify-center">
			<p class={finishedGame ? `text-4xl` : `text-3xl`}>
				{time.m.toString().padStart(2, '0')}:{time.s.toString().padStart(2, '0')}{finishedGame
					? '.' + time.cs.toString().padStart(2, '0')
					: ''}
			</p>
			{#if !finishedGame}
				<p class="break-words">
					{#each text as letter, index}
						<span
							class="spacing border-l-2 border-transparent text-3xl tracking-widest {currentLetterIndex ===
								index && `border-white transition-all`}"
							id="text-{letter.typed}"
						>
							{letter.letter}
						</span>
					{/each}
				</p>
			{:else}
				<h1 class="text-3xl font-semibold">Press enter to play again</h1>
			{/if}
		</div>
	</div>
</div>

<style>
	:root {
		--bg-ctp-mocha: #1e1e2e;
		--bg-nav-ctp-mocha: #181825;
		--text-ctp-mocha: #cdd6f4;
		--text-not-ctp-mocha: #6c7086;
		--text-incorrect-ctp-mocha: #f38ba8;
	}
	#nav {
		background-color: var(--bg-nav-ctp-mocha);
	}
	#container {
		color: var(--text-ctp-mocha);
	}
	#main {
		background-color: var(--bg-ctp-mocha);
	}
	#text-not {
		color: var(--text-not-ctp-mocha);
	}
	#text-incorrect {
		color: var(--text-incorrect-ctp-mocha);
	}
	#text-correct {
		color: var(--text-correct-ctp-mocha);
	}
</style>
