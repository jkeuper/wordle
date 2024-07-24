<script lang="ts">
	import { createEventDispatcher, getContext, onMount } from "svelte";

	import { mode, settings } from "../../stores";
	import { modeData, GameState } from "../../utils";
	import type { Toaster } from "../widgets";
	import Setting from "./Setting.svelte";
    import { GameMode } from "../../enums";

	export let state: GameState;

	const toaster = getContext<Toaster>("toaster");
	const dispatch = createEventDispatcher();

	let root: HTMLElement;
	onMount(() => {
		root = document.documentElement;
	});
	$: {
		if (root) {
			$settings.dark ? root.classList.remove("light") : root.classList.add("light");
			$settings.colorblind
				? root.classList.add("colorblind")
				: root.classList.remove("colorblind");
			localStorage.setItem("settings", $settings.toString());
		}
	}
</script>

<!-- not currently supported, see https://github.com/sveltejs/svelte/issues/3105 -->
<!-- <svelte:body class:light={!$settings.dark} class:colorblind={$settings.colorblind} /> -->
<div class="outer">
	<div class="settings-top">
		<h3>settings</h3>
		<!-- svelte-ignore a11y-click-events-have-key-events -->
		<div
			on:click={() => {
				if (!state.validHard) {
					toaster.pop("Game has already violated hard mode");
				}
			}}
		>
			<Setting type="switch" bind:value={$settings.hard[$mode]} disabled={!state.validHard}>
				<svelte:fragment slot="title">Hard Mode</svelte:fragment>
				<svelte:fragment slot="desc">
					Any revealed hints must be used in subsequent guesses
				</svelte:fragment>
			</Setting>
		</div>
		<Setting type="switch" bind:value={$settings.dark}>
			<svelte:fragment slot="title">Dark Theme</svelte:fragment>
		</Setting>
		<Setting type="switch" bind:value={$settings.colorblind}>
			<svelte:fragment slot="title">Color Blind Mode</svelte:fragment>
			<svelte:fragment slot="desc">High contrast colors</svelte:fragment>
		</Setting>
		<Setting type="dropdown" bind:value={$mode} options={modeData.modes.map((e) => e.name)}>
			<svelte:fragment slot="title">Game Mode</svelte:fragment>
			<svelte:fragment slot="desc">
				The game mode determines how often the word refreshes
			</svelte:fragment>
		</Setting>
		<!-- svelte-ignore a11y-click-events-have-key-events -->
		<Setting type="custom" bind:value={$mode}>
			<svelte:fragment slot="title">Play Historical Game</svelte:fragment>
			<svelte:fragment slot="desc">
				Play a previous word by pasting in a link or setting the date number
			</svelte:fragment>
			<svelte:fragment slot="custom">
				<svg
					class="custom"
					xmlns="http://www.w3.org/2000/svg"
					viewBox="0 0 24 24"
					on:click={() => dispatch("historical")}
				>
					<path
						d="M19.391 12c0-4.082-3.309-7.391-7.391-7.391a7.39 7.39 0 0 0-6.523 3.912l1.653 1.567H2v-5.13l1.572 1.659A9.99 9.99 0 0 1 12 2a10 10 0 1 1 0 20c-4.589 0-8.453-3.09-9.631-7.301l2.512-.703c.871 3.113 3.73 5.395 7.119 5.395 4.082 0 7.391-3.309 7.391-7.391zM12 7.5a1 1 0 0 1 1 1v3.062l3.288 3.031a1 1 0 0 1-1.356 1.471L11 12.438V8.5a1 1 0 0 1 1-1z"
					/>
				</svg>
			</svelte:fragment>
		</Setting>
		<!-- svelte-ignore a11y-click-events-have-key-events -->
		<Setting type="custom" bind:value={$mode}>
			<svelte:fragment slot="title">Create Custom Game</svelte:fragment>
			<svelte:fragment slot="desc">
				Create a custom word by entering a word
			</svelte:fragment>
			<svelte:fragment slot="custom">
				<svg
					class="custom"
					xmlns="http://www.w3.org/2000/svg"
					viewBox="0 0 30 15"
					on:click={() => dispatch("custom")}
				>
					<path
						d="m 9.2975 9.8848 c 0 0.4945 -0.4024 0.8967 -0.8953 0.8967 h -0.6232 c -0.3257 0 -0.6249 -0.1763 -0.7815 -0.462 l -1.5833 -2.8578 c -0.5096 -0.918 -1.073 -2.0297 -1.4938 -3.0369 l -0.0385 0.0124 c 0.0507 1.1345 0.0784 2.3475 0.0784 3.7525 v 1.6966 c 0 0.4929 -0.3993 0.8951 -0.8936 0.8951 c -0.4929 0 -0.8907 -0.4022 -0.8907 -0.8951 v -6.8145 c 0 -0.4943 0.3975 -0.895 0.8907 -0.895 h 0.8566 c 0.3226 0 0.6203 0.1736 0.78 0.4544 l 1.528 2.6962 c 0.5112 0.906 1.0209 1.9809 1.4034 2.948 h 0.0397 c -0.1289 -1.1301 -0.1657 -2.2954 -0.1657 -3.5838 v -1.6199 c 0 -0.4943 0.3991 -0.895 0.8934 -0.895 c 0.4929 0 0.8953 0.4006 0.8953 0.895 v 6.813 z m 6.3781 0.8965 h -3.8923 c -0.4405 0 -0.7968 -0.3592 -0.7968 -0.7984 v -7.0079 c 0 -0.4391 0.3561 -0.7984 0.7968 -0.7984 h 3.7144 c 0.4405 0 0.7953 0.3592 0.7953 0.7984 s -0.3546 0.7985 -0.7953 0.7985 h -2.3509 c -0.1153 0 -0.2073 0.092 -0.2073 0.2087 v 1.3664 c 0 0.117 0.092 0.209 0.2073 0.209 h 2.1679 c 0.4345 0 0.7877 0.3534 0.7877 0.7893 c 0 0.4391 -0.3561 0.7984 -0.7953 0.7984 h -2.1603 c -0.1153 0 -0.2073 0.0925 -0.2073 0.2059 v 1.6246 c 0 0.115 0.092 0.2054 0.2073 0.2054 h 2.5288 c 0.4391 0 0.7954 0.3596 0.7954 0.8018 c 0 0.4393 -0.3563 0.7984 -0.7954 0.7984 z m 12.0732 -8.2405 c 0.1767 0.2284 0.2398 0.5265 0.1676 0.8059 l -1.6646 6.4445 c -0.1504 0.5818 -0.6756 0.9902 -1.276 0.9902 c -0.6311 0 -1.1715 -0.4481 -1.2927 -1.0639 l -0.5204 -2.6395 c -0.1659 -0.8692 -0.3056 -1.6735 -0.4053 -2.6563 h -0.0277 c -0.1521 0.9737 -0.2932 1.7872 -0.4976 2.6563 l -0.5864 2.6534 c -0.1335 0.6126 -0.6787 1.0502 -1.3052 1.0502 c -0.6203 0 -1.1576 -0.4266 -1.3006 -1.0287 l -1.5079 -6.3552 c -0.0722 -0.2932 -0.0015 -0.6064 0.1858 -0.843 c 0.1875 -0.2394 0.4745 -0.3774 0.777 -0.3774 c 0.4792 0 0.8889 0.3439 0.9766 0.8135 l 0.5005 2.7363 c 0.1935 1.0197 0.3716 2.1311 0.5129 3.0004 h 0.0246 c 0.1397 -0.9367 0.3441 -1.9656 0.562 -3.0281 l 0.5204 -2.5178 c 0.1198 -0.5837 0.6342 -1.0027 1.2315 -1.0027 c 0.605 0 1.1241 0.4283 1.236 1.0209 l 0.4943 2.6041 c 0.1937 1.0072 0.3333 1.9284 0.4608 2.8865 h 0.0248 c 0.1289 -0.958 0.3192 -1.9681 0.4972 -2.991 l 0.5528 -2.7667 c 0.0877 -0.4391 0.4715 -0.7556 0.9183 -0.7556 c 0.2915 0 0.565 0.1349 0.7413 0.3639 z"
					/>
				</svg>
			</svelte:fragment>
		</Setting>
		<div class="links">
			<a href="https://github.com/jkeuper/wordle" target="_blank" rel="noreferrer">
				Leave a ⭐
			</a>
			<a href="https://github.com/jkeuper/wordle/issues" target="_blank" rel="noreferrer">
				Report a Bug
			</a>
		</div>
	</div>
</div>

<style>
	.outer {
		display: flex;
		flex-direction: column;
		justify-content: space-between;
	}
	.links {
		font-size: var(--fs-medium);
		border-bottom: 1px solid var(--border-primary);
		color: var(--fg-secondary);
		display: flex;
		justify-content: space-between;
	}
	:global(.settings-top > div) {
		padding: 16px 0;
		border-bottom: 1px solid var(--border-primary);
	}
	.custom {
		height: 2rem;
		fill: var(--fg-secondary);
		cursor: pointer;
	}
</style>
