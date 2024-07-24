<script lang="ts">
	import { COLS, ROWS, words, modeData, seededRandomInt, newSeed } from "../../utils";
	import { getContext } from "svelte";
	import type { Toaster } from ".";

	import { Tile } from "../board";
    import { GameMode } from "../../enums";

	const toaster = getContext<Toaster>("toaster");

	let validWord = false;
	let newWord = "";

	function validateWord() {
		if (newWord.length != COLS) return false;

		return words.contains(newWord);
	}

	function onInput(e: KeyboardEvent & { currentTarget: EventTarget & HTMLInputElement }) {
		if (e.key === "Enter") {
			e.preventDefault();
			e.currentTarget.blur();
			copyLink(e);
		}
	}

	function copyLink(e: MouseEvent | KeyboardEvent) {
		if (!validWord) return;
		let wordNumber = words.words.indexOf(newWord) + seededRandomInt(0, words.words.length, newSeed(GameMode.custom));
		toaster.pop("Copied");
		navigator.clipboard.writeText(`${window.location.href}/${wordNumber}`);
	
		//reset();
	}

	export function reset() {
		newWord = "";
		validWord = false;
	}
</script>

<h3>Create a new custom wordle</h3>
<div>Create a custom wordle game. Note that an existing word must be chosen.</div>
<div>
	<form>
		<input
			type="text"
			bind:value={newWord}
			placeholder="Example: wordl"
			class:valid={validWord}
			on:input={() => (validWord = validateWord())}
			on:keydown={onInput}
		/>
	</form>
</div><div
	class:disabled={!validWord}
	class="button"
	on:click={copyLink}
	on:keydown={copyLink}
>
	copy link
</div>
<div>
	This is a recreation of the original <a
		href="https://www.nytimes.com/games/wordle/"
		target="_blank"
		rel="noreferrer">Wordle</a
	>
	by Josh Wardle with additional modes and features, allowing you to play infinite wordles. Switch
	to infinite mode to play an unlimited number of times.
	<br /><br />
	Open the settings menu to see some of the additional features.
	<br />
	Written with Svelte, in Typescript by
	<a href="https://github.com/MikhaD" target="_blank" rel="noreferrer">MikhaD</a>,
	updated by <a href="https://github.com/jkeuper" target="_blank" rel="noreferrer">Jasper Keuper</a>.
</div>

<style lang="scss">
	div {
		margin: 14px 0;
	}
	.examples {
		border-top: 1px solid var(--border-primary);
		border-bottom: 1px solid var(--border-primary);
		:global(.row > *) {
			height: 100%;
			aspect-ratio: 1;
		}
		&:not(.complete) :global(.row .back) {
			transition-delay: 0.3s;
		}
	}
	.row {
		height: 40px;
		display: flex;
		gap: 4px;
	}
	div {
		text-align: center;
		margin-top: 0.4rem;
	}
	input {
		text-align: center;
		color: inherit;
		font-size: inherit;
		width: 100%;
		height: 100%;
		border-radius: 4px;
		background-color: var(--border-secondary);
		border: none;
		padding: 0.5rem;
		outline: solid 1px var(--red);
	}
	input:placeholder-shown {
		outline: none;
	}
	input::-webkit-outer-spin-button,
	input::-webkit-inner-spin-button {
		-webkit-appearance: none;
		margin: 0;
	}
	input[type="number"] {
		-moz-appearance: textfield;
		appearance: textfield;
	}
	.valid {
		outline-color: var(--color-correct);
	}
	select {
		display: inline;
		background: var(--border-secondary);
	}
	.number {
		display: flex;
		gap: 1rem;
		form {
			flex: 1;
		}
	}
	.button {
		background-color: var(--color-correct);
	}
	.disabled {
		background-color: var(--fg-secondary);
		cursor: default;
		pointer-events: none;
		&:hover {
			opacity: 1;
		}
	}
</style>
