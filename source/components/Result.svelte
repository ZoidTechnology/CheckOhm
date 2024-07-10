<script lang="ts">
	export let resolution: number;
	export let reference: number;
	export let referenceError: number;
	export let measurement: number;
	export let measurementError: number;

	const PADDING = 0.2;

	let valid: boolean;
	let marker: number;
	let gradient: string;
	let inconclusiveRange: string;
	let passRange: string;

	function format(value: number, resolution: number) {
		if (resolution) {
			value = Math.round(value * (1 / resolution)) * resolution;
		}

		let exponent = Math.floor(Math.log10(Math.abs(value)) / 3);
		exponent = Math.min(Math.max(-1, exponent), 3);
		const divisor = Math.pow(10, exponent * 3);
		const scaled = Number((value / divisor).toPrecision(10));
		const prefix = scaled ? ['m', '', 'k', 'M', 'G'][exponent + 1] : '';
		return scaled + prefix + 'Ω';
	}

	$: {
		const difference = measurement - reference;
		const inconclusive = referenceError + measurementError;
		const range = Math.max(Math.abs(difference), inconclusive) / (1 - PADDING);
		valid = range !== 0 && isFinite(range);

		if (valid) {
			const pass = Math.max(0, measurementError - referenceError);
			const fail = range - inconclusive;
			marker = (difference / range) * 50 + 50;
			gradient = 'linear-gradient(90deg';
			let position = 0;
			let currentColor = '';

			function addStep(color: string, width: number) {
				if (width === 0) {
					return;
				}

				color = ', var(--' + color + ') ';

				if (color !== currentColor) {
					if (currentColor) {
						gradient += currentColor + position + '%';
					}
					gradient += color + position + '%';
				}

				currentColor = color;
				position += (width / range) * 50;
			}

			addStep('red', fail);
			addStep('orange', inconclusive - pass);
			addStep('green', pass * 2);
			addStep('orange', inconclusive - pass);
			addStep('red', fail);

			gradient += currentColor + position + '%)';
			inconclusiveRange = '';

			if (inconclusive - pass) {
				inconclusiveRange += format(reference - inconclusive, resolution);
				inconclusiveRange += '&ndash;';
				inconclusiveRange += format(reference + inconclusive, resolution);
			}

			passRange = '';

			if (pass) {
				passRange += format(reference - pass, resolution);
				passRange += '&ndash;';
				passRange += format(reference + pass, resolution);
			}
		}
	}
</script>

{#if valid}
	<div class="result">
		<div class="marker" style="left: {marker}%;"></div>
		<div class="range" style="background: {gradient};"></div>
		<div class="legend">
			<div style="--color: var(--red);">Fail</div>
			{#if inconclusiveRange}
				<div style="--color: var(--orange);">Inconclusive ({@html inconclusiveRange})</div>
			{/if}
			{#if passRange}
				<div style="--color: var(--green);">Pass ({@html passRange})</div>
			{/if}
		</div>
	</div>
{/if}

<style>
	.result {
		position: relative;
	}

	.marker {
		position: absolute;
		top: calc(-1 * var(--unit));
		transform: translateX(-50%);
		outline: 1px solid var(--background);
		border-radius: 1px;
		background-color: var(--foreground);
		width: 2px;
		height: calc(var(--unit) * 4);
	}

	.range {
		margin-bottom: calc(var(--unit) * 2);
		border-radius: var(--unit);
		height: calc(var(--unit) * 2);
	}

	.legend {
		display: flex;
		gap: calc(var(--unit) * 2);
		color: var(--foreground-alternate);
	}

	.legend div {
		display: flex;
		align-items: center;
		gap: var(--unit);
	}

	.legend div::before {
		flex: none;
		border-radius: 100%;
		background-color: var(--color);
		width: var(--unit);
		height: var(--unit);
		content: '';
	}
</style>
