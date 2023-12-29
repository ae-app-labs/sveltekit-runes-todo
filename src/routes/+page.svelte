<script>
    // Filter State
    let filter = $state('all')

    // Todo State - declare todos as a reactive state
    let todos = $state([])
    let filteredTodos = $derived( filterTodos() )

    $effect( () => {
       // console.log(todos)
    })

    // Load the todos from saved state
    $effect( () => {
        const savedTodos = localStorage.getItem('todos')
        savedTodos && (todos = JSON.parse(savedTodos))
    })

    // Save the todos to localStorage whenever it is updated
    $effect( () => {
        localStorage.setItem('todos', JSON.stringify(todos))
    })

    function filterTodos() {
        console.log(filter, todos)
        switch(filter){
            case 'all':
                return todos;
            case 'active':
                return todos.filter( todo => !todo.done)
            case 'completed':
                const completedTodos = todos.filter( (todo) => todo.done)
                console.log(completedTodos)
                return completedTodos
        }
    }

    const addTodo = (event) => {
        if(event.key != 'Enter') return

        const todoEl = event.target;
        const id = crypto.randomUUID()
        todos.push({text: todoEl.value, done: false, itemId:id})

        todoEl.value = ''
    }

    const editTodo = (event) => {
        const inputEl = event.target;
        const index = inputEl.dataset.index
        todos[index].text = inputEl.value;
    }

    const toggleTodo = (event) => {
        const inputEl = event.target;
        const index = inputEl.dataset.index
        todos[index].done = !todos[index].done
    }

    const setFilter = (newFilter) => {
        filter = newFilter
    }

    const remaining = () => {
        return todos.filter( todo => !todo.done).length
    }

</script>

<input type="text" placeholder="Add todo" onkeydown={addTodo}>

<div class="todos">
    {#each filteredTodos as todo,i}
        <div class="todo" class:completed={todo.done}>
            <input type="text" value="{todo.text}" oninput={editTodo} data-itemid={todo.itemId} data-index={i}>
            <input type="checkbox" checked={todo.done} onchange={toggleTodo} data-itemid={todo.itemId} data-index={i}>
        </div>
    {/each}
</div>

<div class="filters">
    {#each ['all', 'active', 'completed'] as filter}
    <button onclick={() => setFilter(filter)}>{filter}</button>
    {/each}
</div>

<p>{remaining()} remaining</p>

<style>
    .todos {
        display: grid;
        gap: 1rem;
        margin-block-start: 1rem;
    }

    .todo {
        position: relative;
    }

    .completed {
        opacity: 0.4;
        transition: opacity 0.3;
    }

    .filters {
        margin-block: 1rem;
        display: flex;
        gap: .5rem;
    }

    input[type="text"] {
        width: 100%;
        padding: 1rem;
    }

    input[type="checkbox"]{
        position: absolute;
        right: 4%;
        top: 50%;
        translate: 0% -50%;
    }
</style>