<script context="module" lang="ts">
	import {
		modeData,
		seededRandomInt,
		Stats,
		GameState,
		Settings,
		LetterStates,
		getWordNumber,
		words,
		newSeed
	} from "./utils";
	import Game from "./components/Game.svelte";
	import { letterStates, settings, mode } from "./stores";
	import { GameMode } from "./enums";
	import { Toaster } from "./components/widgets";
	import { setContext } from "svelte";
    import { to_number } from "svelte/internal";

	document.title = "Wordle+ | An infinite word guessing game";
</script>

<script lang="ts">
	export let version: string;
	setContext("version", version);
	localStorage.setItem("version", version);
	let stats: Stats;
	let word: string;
	let state: GameState;
	let toaster: Toaster;
	let game: Game;

	settings.set(new Settings(localStorage.getItem("settings")));
	settings.subscribe((s) => localStorage.setItem("settings", JSON.stringify(s)));

	function initFromUrl() {
		game?.reset();
		
		const hash = window.location.hash.slice(1).split("/");
		let modeVal: GameMode = !isNaN(GameMode[hash[0]])
			? GameMode[hash[0]]
			: /*+localStorage.getItem("mode") ||*/ modeData.default;

		mode.set(modeVal, true);

		// If this is a link to a specific word make sure that that is the word
		if (!isNaN(+hash[1]) && (+hash[1] < getWordNumber(modeVal) || modeVal === GameMode.custom)) {
			modeData.modes[modeVal].seed =
				(+hash[1] - 1) * modeData.modes[modeVal].unit + modeData.modes[modeVal].start;
			modeData.modes[modeVal].historical = true;
		}
	}

	function routeChange() {
		initFromUrl();
	}
	
	initFromUrl();

	mode.subscribe((m) => {
		localStorage.setItem("mode", `${m}`);
		window.location.hash = GameMode[m];
		stats = new Stats(localStorage.getItem(`stats-${m}`) || m);

		if (m === GameMode.custom) {
			if (modeData.modes[m].seed == 1230940800000) {
				word = '';
			} else {
				const seed = (modeData.modes[m].seed - modeData.modes[m].start) + 1
				word = words.words[seed - seededRandomInt(0, words.words.length, 1230940800000)];
			}
		} else {
			word = words.words[seededRandomInt(0, words.words.length, modeData.modes[m].seed)];
		}
		
		if (modeData.modes[m].historical) {
			state = new GameState(m, localStorage.getItem(`state-${m}-h`));
		} else {
			state = new GameState(m, localStorage.getItem(`state-${m}`));
		}
		// Set the letter states when data for a new game mode is loaded so the keyboard is correct
		letterStates.set(new LetterStates(state.board));
	});

	$: saveState(state);

	function saveState(state: GameState) {
		if (modeData.modes[$mode].historical) {
			localStorage.setItem(`state-${$mode}-h`, state.toString());
		} else {
			localStorage.setItem(`state-${$mode}`, state.toString());
		}
	}
</script>

<Toaster bind:this={toaster} />
<svelte:window on:hashchange={routeChange} />
{#if toaster}
	<Game {stats} bind:this={game} bind:word {toaster} bind:game={state} />
{/if}
