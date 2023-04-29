<script lang="ts" context="module">
  export type Todo = { id: number, done: any; text: any };

</script>
<script lang="ts">
	import { createEventDispatcher } from 'svelte'
	// import { fade } from 'svelte/transition'
  
	export let todo: Todo

	const dispatch = createEventDispatcher()
</script>

<div class="wrapper">
	<!-- <h2>Note:</h2> -->
	<div class="note">
		<input type="checkbox" checked={todo.done} on:change={() => dispatch('toggleDone')} />
		<span class:done={todo.done}>{todo.text}</span>
		<button on:click={() => dispatch('delete')}>X</button>
	</div>
</div>

<style>
	.done {
		color: gray;
		text-decoration: line-through;
	}

	.wrapper {
		width: 20rem;
		padding-right: 5rem;
	}

	/* h2 {
		font-size: 1rem;
	} */

	.note {
		padding-left: 1rem;
		display: flex;
		align-items: center;
		gap: 0.75rem;
	}

	button {
		padding: 0.2rem;
		line-height: 0.9;
	}

	input {
		width: 1rem;
		height: 1rem;
		margin: 0;
	}
</style>
