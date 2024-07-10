<script lang="ts">
	export let title: string;
	export let description: string;
</script>

<div class="section">
	<div>{title}</div>
	<div class="input">
		<slot />
	</div>
	<div class="description">{description}</div>
</div>

<style>
	.input {
		display: flex;
		align-items: center;
		gap: var(--unit);
		margin: calc(var(--unit) * 1) 0;
	}

	.description {
		color: var(--foreground-alternate);
	}
</style>
