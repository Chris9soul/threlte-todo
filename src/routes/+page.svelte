<script lang="ts">
	import { Canvas } from '@threlte/core'
	import Scene from './Scene.svelte'

</script>

<div id="app">
	<Canvas>
		<Scene />
	</Canvas>
</div>

<style>
	:global(body) {
		background-color: rgb(3, 8, 29);
    color: black;
    font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
	}

	#app {
		height: 100vh;
    overflow: hidden;
    position: fixed;
    inset: 0;
    position: relative;
	}
</style>
