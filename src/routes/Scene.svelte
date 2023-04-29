<script lang="ts">
	import { T, useFrame } from '@threlte/core'
	import { HTML, Instance, InstancedMesh, OrbitControls, Text } from '@threlte/extras'
	import { degToRad } from 'three/src/math/MathUtils'
	import TodoCard, { type Todo } from './TodoCard.svelte'

	let rotation = 0
	useFrame((state, delta) => {
		rotation += delta
	})

	let lastId = 0

	// This is a function that creates a todo object.
	const createTodo = (text: string, done = false) => ({ id: ++lastId, text, done })

	let todoText = ''

	// The app starts with two todos already created.
	let todos = [createTodo('Learn Svelte', true), createTodo('Learn Threlte')]

	// This is recomputed when the todos array changes.
	$: uncompletedCount = todos.filter((t) => !t.done).length

	// This is recomputed when the todos array or uncompletedCount changes.
	$: status = `${uncompletedCount} of ${todos.length} remaining`

	function addTodo() {
		todos.push(createTodo(todoText))
		todos = todos // triggers reactivity
		todoText = '' // clears the input
	}

	// This deletes the todo with a given id.
	const deleteTodo = (todoId: number) => {
		todos = todos.filter((t) => t.id !== todoId)

	}

	function toggleDone(todo: Todo) {
		const { id } = todo
		todos = todos.map((t) => (t.id === id ? { ...t, done: !t.done } : t))
	}

</script>

<T.OrthographicCamera
	makeDefault
	position={[-3, 0, 10]}
  left={-2}
  right={2}
  top={2}
  bottom={-2}
  near={1}
  far={100}
	zoom={40}
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
<!-- <HTML transform position.y={2}>
	<h1>Threlte Todo app</h1>
</HTML> -->
<T.Mesh position={[0, -1, 0]} rotation.y={degToRad(0)}>
	<T.BoxGeometry args={[12, 3, 1]} />
  <T.MeshStandardMaterial color="#fe3d00"/>
  <HTML transform position.z={0.5}>
    <form on:submit|preventDefault={addTodo}>
      <input size="30" placeholder="Learn Threlte..." bind:value={todoText} />
      <!-- The Add button is disabled if no text
           has been entered in the input. -->
      <button disabled={!todoText}>Add</button>
    </form>
  </HTML>
</T.Mesh>

<InstancedMesh position.y={-5}>
	<T.BoxGeometry args={[10, 3, 0.2]} />
	<T.MeshStandardMaterial color="#fff"/>
	{#each todos as todo, index}
		<Instance position={[0, -(3.5 * index), 0]} rotation.y={degToRad(0)}>
			<HTML transform position={[0, 0.5, 0]}>
				<TodoCard
					{todo}
					on:delete={() => deleteTodo(todo.id)}
					on:toggleDone={() => toggleDone(todo)}
					/>
			</HTML>
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