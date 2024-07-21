<script lang="ts">
	import { failed, GameState } from "../../../utils";

	export let game: GameState;
</script>

<h3>guesses</h3>
<div class="container">
	<span class="guess">Wordle guessed in {game.guesses}/6!</span>
	<span class="guess" style="font-size: 32px;">
		{#each game.board.state.slice(0, game.guesses).map((r) => r.join("")) as row}
			{row}<br>
		{/each}
	</span>
</div>

<style>
	.guess {
		display: grid;
		place-items: center;
	}
	.container {
		width: 80%;
		margin-left: auto;
		margin-right: auto;
		display: flex;
		flex-direction: column;
		gap: 4px;
	}
	.graph {
		height: 20px;
		display: flex;
		gap: 4px;
	}
	.bar {
		min-width: 7%;
		transition: width 0.3s ease-out;
		background: var(--color-absent);
		color: white;
		font-weight: bold;
		display: flex;
		justify-content: end;
		align-items: center;
		padding-right: min(0.8rem, 1vw);
	}
	.bar.this {
		background: var(--color-correct);
	}
</style>
