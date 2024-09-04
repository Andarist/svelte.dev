---
title: svelte/action
---

Actions are functions that are called when an element is created. They can return an object with a `destroy` method that is called after the element is unmounted:

```svelte
<!--- file: App.svelte --->
<script>
	/** @type {import('svelte/action').Action}  */
	function foo(node) {
		// the node has been mounted in the DOM

		return {
			destroy() {
				// the node has been removed from the DOM
			}
		};
	}
</script>

<div use:foo />
```

An action can have a parameter. If the returned value has an `update` method, it will be called immediately after Svelte has applied updates to the markup whenever that parameter changes.

> Don't worry that we're redeclaring the `foo` function for every component instance — Svelte will hoist any functions that don't depend on local state out of the component definition.

```svelte
<!--- file: App.svelte --->
<script>
	/** @type {string} */
	export let bar;

	/** @type {import('svelte/action').Action<HTMLElement, string>}  */
	function foo(node, bar) {
		// the node has been mounted in the DOM

		return {
			update(bar) {
				// the value of `bar` has changed
			},

			destroy() {
				// the node has been removed from the DOM
			}
		};
	}
</script>

<div use:foo={bar} />
```

## Attributes

Sometimes actions emit custom events and apply custom attributes to the element they are applied to. To support this, actions typed with `Action` or `ActionReturn` type can have a last parameter, `Attributes`:

```svelte
<!--- file: App.svelte --->
<script>
	/**
	 * @type {import('svelte/action').Action<HTMLDivElement, { prop: any }, { 'on:emit': (e: CustomEvent<string>) => void }>}
	 */
	function foo(node, { prop }) {
		// the node has been mounted in the DOM

		//...LOGIC
		node.dispatchEvent(new CustomEvent('emit', { detail: 'hello' }));

		return {
			destroy() {
				// the node has been removed from the DOM
			}
		};
	}
</script>

<div on:emit={handleEmit} use:foo={{ prop: 'someValue' }} />
```

## Types



### Action



Actions are functions that are called when an element is created.
You can use this interface to type such actions.
The following example defines an action that only works on `<div>` elements
and optionally accepts a parameter which it has a default value for:
```ts
export const myAction: Action<HTMLDivElement, { someProperty: boolean } | undefined> = (node, param = { someProperty: true }) => {
	// ...
}
```
`Action<HTMLDivElement>` and `Action<HTMLDivElement, undefined>` both signal that the action accepts no parameters.

You can return an object with methods `update` and `destroy` from the function and type which additional attributes and events it has.
See interface `ActionReturn` for more details.

Docs: https://svelte.dev/docs/svelte-action

<div class="ts-block">

```dts
interface Action<
	Element = HTMLElement,
	Parameter = undefined,
	Attributes extends Record<string, any> = Record<
		never,
		any
	>
> {/*…*/}
```

<div class="ts-block-property">

```dts
<Node extends Element>(
	...args: undefined extends Parameter
		? [node: Node, parameter?: Parameter]
		: [node: Node, parameter: Parameter]
): void | ActionReturn<Parameter, Attributes>;
```

<div class="ts-block-property-details"></div>
</div></div>

### ActionReturn



Actions can return an object containing the two properties defined in this interface. Both are optional.
- update: An action can have a parameter. This method will be called whenever that parameter changes,
	immediately after Svelte has applied updates to the markup. `ActionReturn` and `ActionReturn<undefined>` both
	mean that the action accepts no parameters.
- destroy: Method that is called after the element is unmounted

Additionally, you can specify which additional attributes and events the action enables on the applied element.
This applies to TypeScript typings only and has no effect at runtime.

Example usage:
```ts
interface Attributes {
	newprop?: string;
	'on:event': (e: CustomEvent<boolean>) => void;
}

export function myAction(node: HTMLElement, parameter: Parameter): ActionReturn<Parameter, Attributes> {
	// ...
	return {
		update: (updatedParameter) => {...},
		destroy: () => {...}
	};
}
```

Docs: https://svelte.dev/docs/svelte-action

<div class="ts-block">

```dts
interface ActionReturn<
	Parameter = undefined,
	Attributes extends Record<string, any> = Record<
		never,
		any
	>
> {/*…*/}
```

<div class="ts-block-property">

```dts
update?: (parameter: Parameter) => void;
```

<div class="ts-block-property-details"></div>
</div>

<div class="ts-block-property">

```dts
destroy?: () => void;
```

<div class="ts-block-property-details"></div>
</div></div>

