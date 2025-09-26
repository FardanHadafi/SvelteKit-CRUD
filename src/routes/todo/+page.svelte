<script lang="ts">
	// Buat interface untuk data todo agar tipe datanya konsisten
	interface Todo {
		id: number;
		text: string;
		completed: boolean;
	}

	// Buat variables yang diperlukan
	let todos: Todo[] = [];
	let newTodoText = '';
	let editTodoId: number | null = null;
	let editText = '';

	// addTodo
	function addTodo() {
		// Validasi user input supaya tidak menerima input kosong
		if (!newTodoText.trim()) return;
		// Buat variable todo baru sebagai todo yang akan ditambahkan ke list
		const newTodo: Todo = {
			id: Date.now(), // Membuat unique ID karna masing2 list tidak boleh mempunyai ID yang sama
			text: newTodoText.trim(), // Text todo baru diambil dari user input melalui input field
			completed: false // Completed false by default
		};
		todos = [...todos, newTodo]; // Re-assigning variable todo diawal dengan todo baru dari user input
		newTodoText = ''; // Membersihkan user input setelah user berhasil memasukan todonya
	}

	// deleteTodo
	function deleteTodo(id: number) {
		todos = todos.filter((todo) => todo.id !== id);
	}

	// editTodo
	function editTodo(todo: Todo) {
		editTodoId = todo.id;
		editText = todo.text;
	}

	// saveTodo
	function saveTodo(id: number) {
		// Ambil data todo yang tersedia
		const todoToSave = todos.find((todo) => todo.id === id);
		if (todoToSave && editText.trim()) {
			todoToSave.text = editText.trim();
			todos = todos;
		}
		cancelEdit();
	}

	// cancelEdit
	function cancelEdit() {
		editTodoId = null;
		editText = '';
	}

	// Memfilter todo mana yang belum terselesaikan
	$: incompleteTodos = todos.filter((todo) => !todo.completed).length;
</script>

<main>
	<!--Header-->
	<h1>SvelteKit TodoApp</h1>
	<p>You have <strong>{incompleteTodos}</strong> incomplete task(s)</p>

	<!--Todo Form-->
	<form on:submit|preventDefault={addTodo}>
		<input
			type="text"
			bind:value={newTodoText}
			placeholder="What needs to be done ?"
			aria-label="Add a new todo"
		/>
		<button type="submit">Add</button>
	</form>

	<!--Render the todo lists-->
	<ul>
		{#each todos as todo (todo.id)}
			<li>
				{#if editTodoId === todo.id}
					<input
						type="text"
						bind:value={editText}
						on:keydown={(e) => e.key === 'Enter' && saveTodo(todo.id)}
						on:keydown={(e) => e.key === 'Escape' && cancelEdit()}
						aria-label="Edit todo"
					/>
					<div>
						<button on:click={() => saveTodo(todo.id)}>Save</button>
						<button on:click={() => cancelEdit()}>Cancel</button>
					</div>
				{:else}
					<div>
						<input type="checkbox" bind:checked={todo.completed} aria-label="Mark as complete" />
						<span>{todo.text}</span>
					</div>
					<div>
						<button on:click={() => editTodo(todo)}>Edit</button>
						<button on:click={() => deleteTodo(todo.id)}>Delete</button>
					</div>
				{/if}
			</li>
		{/each}
	</ul>
</main>

<style>
	/**Add Your Style Here*/
</style>
