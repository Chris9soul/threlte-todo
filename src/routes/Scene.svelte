<script lang="ts">
	import { T } from '@threlte/core'
	import { HTML, Instance, InstancedMesh, OrbitControls, Text, createTransition, transitions } from '@threlte/extras'
	import { cubicOut } from 'svelte/easing'
	import { degToRad } from 'three/src/math/MathUtils'
	import TodoCard, { type Todo } from './TodoCard.svelte'
	import type { Mesh } from 'three'
	
	transitions()

	const scale = createTransition<Mesh>((ref) => {
    return {
      tick(t: number) {
        // t is [0,1] and needs to be converted to [0.5,1]
        t = 0.5 + t * 0.5
        ref.scale.set(t, t, t)
      },
      easing: cubicOut,
      duration: 400
    }
  })

	let screenSize: number
	
	let lastId = 0
	let todoText = ''
	let shape = 'box'
	
	const createTodo = (text: string, done = false, shape: string) => ({ id: ++lastId, text, done, shape })

	let todos = [createTodo('Learn Svelte', true, 'box'), createTodo('Learn Threlte', false, 'sphere')]

	function addTodo() {
		todos.push(createTodo(todoText, false, shape))
		todos = todos 
		todoText = '' 
	}

	const deleteTodo = (todoId: number) => {
		todos = todos.filter((t) => t.id !== todoId)
	}

	function toggleDone(todo: Todo) {
		const { id } = todo
		todos = todos.map((t) => (t.id === id ? { ...t, done: !t.done } : t))
	}

</script>

<svelte:window bind:innerWidth={screenSize} />

<T.OrthographicCamera
	makeDefault
	position={[-3, 0, 10]}
  left={-2}
  right={2}
  top={2}
  bottom={-2}
  near={1}
  far={100}
	zoom={screenSize < 390 ? 30 : 40}
	on:create={({ ref }) => {
    ref.lookAt(0, -6, 0)
  }}
>
  <OrbitControls
      enableDamping
      target.y={-6}
      maxPolarAngle={degToRad(130)}
      minPolarAngle={degToRad(50)}
      maxAzimuthAngle={degToRad(30)}
      minAzimuthAngle={degToRad(-30)}
      enableZoom={false}
			enablePan={false}
    />
</T.OrthographicCamera>

<T.DirectionalLight color="#fff" intensity={4} position={[3, 10, 7]} />
<T.AmbientLight color="#fff" intensity={0.8}/>

<Text 
	text="Threlte Todo app" 
	fontSize={1} 
	position.y={2} 
	anchorX="center" 
	outlineWidth={0.2} 
	outlineColor="#fe3d00"
	
/>

<T.Mesh position={[0, -1, 0]} rotation.y={degToRad(0)}>
	<T.BoxGeometry args={[12, 3, 1]} />
  <T.MeshStandardMaterial color="#fe3d00"/>
  <HTML transform position.z={0.5}>
    <form on:submit|preventDefault={addTodo}>
      <input size="30" placeholder="Learn Threlte..." bind:value={todoText} />
      <select bind:value={shape}>
				<option value="box">■</option>
				<option value="sphere">●</option>
				<option value="cone">▲</option>
			</select>
      <button disabled={!todoText}>Add</button>
    </form>
  </HTML>
</T.Mesh>

<InstancedMesh position.y={-5}>
	<T.BoxGeometry args={[10, 3, 0.2]} />
	<T.MeshStandardMaterial color="#fff"/>
	{#each todos as todo, index (todo.id)}
		<Instance transition={scale} position={[0, -(3.5 * index), 0]} rotation.y={degToRad(0)}>
			<HTML transform position={[0, 0.5, 0]}>
				<TodoCard
					{todo}
					on:delete={() => deleteTodo(todo.id)}
					on:toggleDone={() => toggleDone(todo)}
					/>
			</HTML>
			<T.Mesh position={[4.3, 0.8, 0]}>
				<T.MeshStandardMaterial color={todo.done ? 'gray' : '#fe3d00'} />
				{#if todo.shape === 'box'}
				<T.BoxGeometry args={[1, 1, 1]} />
				{:else if todo.shape === 'sphere'}
				<T.SphereGeometry args={[0.5, 16, 16]} />
				{:else if todo.shape === 'cone'}
				<T.ConeGeometry args={[0.5, 1, 16]} />
				{/if}
			</T.Mesh>
		</Instance>
		{/each}
	</InstancedMesh>

<style>

	form {
		background-color: #fff8f0;
		display: flex;
		gap: 1rem;
		padding: 1rem;
		border-radius: 0.5rem;
    max-width: 90vw;
	}

	input {
		padding: 0.5rem 0.5rem;
		font-size: 1.2rem;
    max-width: 30vw;
		min-width: 8rem;

	}

	button {
		background-color: #fe3d00;
		color: white;
		padding: 0.25rem 1rem;
		border: none;
		border-radius: 0.2rem;
		cursor: pointer;
		transition: background-color 200ms ease;
	}
	button:disabled {
		background-color: grey;
		cursor:not-allowed
	}

</style>
