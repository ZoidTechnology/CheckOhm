<script lang="ts" context="module">
	export type Units = [string, string][];
</script>

<script lang="ts">
	import Input from './Input.svelte';

	export let units: Units;
	export let value: number;
	export let unit: number;

	function update(event: KeyboardEvent) {
		const key = event.key.toLowerCase();

		for (const [index, [, unitKey]] of units.entries()) {
			if (key === unitKey) {
				unit = index;
			}
		}
	}
</script>

<Input bind:value on:keydown={update} />
<select bind:value={unit}>
	{#each units as [unitName], index}
		<option value={index}>{unitName}</option>
	{/each}
</select>

<style>
	select {
		flex: 1;
		box-sizing: content-box;
		outline: none;
		border: 1px solid var(--foreground-alternate);
		border-radius: var(--unit);
		background-color: var(--background-alternate);
		padding: 0 var(--unit);
		height: calc(var(--unit) * 4);
		color: var(--foreground);
		font-size: inherit;
		font-family: inherit;
	}

	select:focus {
		border-color: var(--foreground);
	}
</style>
