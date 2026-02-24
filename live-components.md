# LiveComponents

Build reactive, real-time interfaces without writing JavaScript. Server-side components with automatic state synchronization.

## Creating a Component

```java
@LiveComponentImpl
public class Counter extends LiveComponent
{
    @State
    private int count = 0;

    public void increment() { count++; }
    public void decrement() { count--; }

    public int getCount() { return count; }

    @Override
    public String template() {
        return "components/counter.html";
    }
}
```

## Component Template

```html
<!-- views/components/counter.html -->
<div live:id="{{ _id }}">
    <h2>Count: {{ count }}</h2>

    <button live:click="increment">+ Increment</button>
    <button live:click="decrement">- Decrement</button>

    <div live:loading>Updating...</div>
</div>
```

## Using a Component in a Page

```twig
{% extends "layout/base.html" %}

{% block content %}
    <h1>My Page</h1>
    {{ component('Counter') | raw }}
{% endblock %}
```

## Actions with Parameters

```java
@LiveComponentImpl
public class TodoList extends LiveComponent
{
    @State
    private List<Todo> todos = new ArrayList<>();

    public void toggleTodo(int id) {
        todos.stream()
            .filter(t -> t.id == id)
            .findFirst()
            .ifPresent(t -> t.completed = !t.completed);
    }

    public void deleteTodo(int id) {
        todos.removeIf(t -> t.id == id);
    }

    public List<Todo> getTodos() { return todos; }

    @Override
    public String template() { return "components/todo-list.html"; }
}
```

```html
<div live:id="{{ _id }}">
    {% for todo in todos %}
    <div>
        <span>{{ todo.text }}</span>
        <button live:click="toggleTodo({{ todo.id }})">Toggle</button>
        <button live:click="deleteTodo({{ todo.id }})">Delete</button>
    </div>
    {% endfor %}
</div>
```

## Real-Time Search with `live:model`

```java
@LiveComponentImpl
public class SearchFilter extends LiveComponent
{
    @State
    private String search = "";

    private final List<String> items = List.of(
        "React", "Vue", "Angular", "Svelte", "Node.js", "Django", "Spring Boot"
    );

    public List<String> getFilteredItems() {
        if (search == null || search.isEmpty()) return items;
        return items.stream()
            .filter(item -> item.toLowerCase().contains(search.toLowerCase()))
            .collect(Collectors.toList());
    }

    public String getSearch() { return search; }

    @Override
    public String template() { return "components/search.html"; }
}
```

```html
<div live:id="{{ _id }}">
    <input
        live:model="search"
        live:debounce="300"
        type="text"
        placeholder="Search..."
        value="{{ search }}"
    >

    <div>{{ filteredItems.size() }} results</div>

    {% for item in filteredItems %}
        <div>{{ item }}</div>
    {% endfor %}
</div>
```

## Auto-Refresh with `live:poll`

```java
@LiveComponentImpl
public class Dashboard extends LiveComponent
{
    @State
    private int activeUsers = 0;

    public void loadInitialData() {
        activeUsers = userService.getActiveCount();
    }

    public void refreshStats() {
        activeUsers = userService.getActiveCount();
    }

    public int getActiveUsers() { return activeUsers; }

    @Override
    public String template() { return "components/dashboard.html"; }
}
```

```html
<div
    live:id="{{ _id }}"
    live:init="loadInitialData"
    live:poll.5s="refreshStats"
>
    <h2>Active Users: {{ activeUsers }}</h2>
    <p>Auto-updates every 5 seconds</p>

    <div live:loading.class="opacity-100" class="opacity-0">
        Updating...
    </div>
</div>
```

## Available Directives

```html
<!-- Click actions -->
<button live:click="save">Save</button>
<button live:click="delete({{ id }})">Delete</button>

<!-- Two-way binding -->
<input live:model="search" type="text">
<input live:model="email" live:blur type="email">
<input live:model="username" live:lazy type="text">
<input live:model="query" live:debounce="500" type="text">

<!-- Auto-refresh -->
<div live:poll="5s">...</div>
<div live:poll.10s="refreshData">...</div>

<!-- Lifecycle -->
<div live:init="loadData">...</div>

<!-- Confirmation -->
<button live:confirm="Are you sure?">Delete</button>

<!-- Loading states -->
<div live:loading>Loading...</div>
<div live:loading.class="opacity-100">...</div>
<button live:loading.add="opacity-50">Save</button>
```

## Setup

LiveComponents require the Obsidian JavaScript client to be included in your layout:

```html
<!DOCTYPE html>
<html>
<head>
    <title>My App</title>
</head>
<body>
    {% block content %}{% endblock %}

    <script src="/js/obsidian-components.js"></script>
</body>
</html>
```
