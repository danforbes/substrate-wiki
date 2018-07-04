# Style Guide for Rust in Polkadot

- Indent using tabs
- Never have spaces before a non-whitespace character starts
- Follow-on lines are only ever a single indent from the original line
- `where` is indented, and its items are indented one further
- Long arg lists or function calls are indented similarly to code blocks
- Always end last item of a multi-line comma-delimited set with `,` when legal:
```
struct Point<T> {
	x: T,
	y: T,
}
```

Single line comma-delimited items do not have a trailing `,`:

```
enum Meal { Breakfast, Lunch, Dinner };
```

- Avoid trailing `;`s where undeeded (e.g. `if condition { return 1 }` rather than `if condition { return 1; }`.
- `match` arms may be