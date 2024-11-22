<script lang="ts">
	import Logo from './Logo.svelte';
	import { onMount } from 'svelte';

	const initialText: string = 'qwertyuiopasdfghjklzxcvbnm';

	type letter = {
		letter: string;
		typed: string;
	};

	let text: letter[] = $state([]);
	let currentTextIndex: number = $state(0);

	const initGame = () => {
		text = [];
		[...initialText].forEach((letter) => {
			text.push({
				letter: letter,
				typed: 'not'
			});
		});
	};

	const handleKeyDown = ({ key }: KeyboardEvent) => {
		text.map((l, i) => {
			if (i === currentTextIndex) {
				if (l.letter === key) {
					l.typed = 'correct';
				} else if (key !== 'Backspace') {
					l.typed = 'incorrect';
				}
			} else if (i === currentTextIndex - 1 && key === 'Backspace') {
				l.typed = 'not';
			}
			return l;
		});
		if (key === 'Backspace') {
			if (currentTextIndex !== 0) {
				currentTextIndex--;
			}
		} else if (currentTextIndex !== text.length) {
			currentTextIndex++;
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
		{#each text as letter, index}
			<span
				class="letter spacing border-l-2 border-transparent text-xl tracking-wider {currentTextIndex ===
					index && `border-white transition-all`}"
				id="text-{letter.typed}"
			>
				{letter.letter}
			</span>
		{/each}
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
