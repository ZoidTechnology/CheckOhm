<script lang="ts">
	export let value: number;
	let invalid = true;

	function update(event: Event) {
		const target = event.target as HTMLInputElement;
		target.value = target.value.replace(/[^0-9.]/g, '');
		value = parseFloat(target.value);
		invalid = isNaN(value);
		invalid ||= target.value.replace(/[^.]/g, '').length > 1;

		if (invalid) {
			value = NaN;
		}
	}
</script>

<input type="text" class:invalid on:keydown on:input={update} />

<style>
	input {
		flex: 1;
		outline: none;
		border: 1px solid var(--foreground-alternate);
		border-radius: var(--unit);
		background-color: var(--background-alternate);
		padding: 0 var(--unit);
		min-width: 0;
		height: calc(var(--unit) * 4);
		color: var(--foreground);
		font-size: inherit;
		font-family: inherit;
	}

	input:focus {
		border-color: var(--foreground);
	}

	.invalid {
		border-color: var(--red-alternate);
	}

	.invalid:focus {
		border-color: var(--red);
	}
</style>
