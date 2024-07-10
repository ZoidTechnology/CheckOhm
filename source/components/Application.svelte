<script lang="ts">
	import DimensionedInput, {type Units} from './DimensionedInput.svelte';
	import Logo from './Logo.svelte';
	import Result from './Result.svelte';
	import Section from './Section.svelte';

	const RESOLUTION_UNITS: Units = [
		['Counts', 'c'],
		['Digits', 'd']
	];
	const RESISTANCE_UNITS: Units = [
		['Ω', 'r'],
		['kΩ', 'k'],
		['MΩ', 'm']
	];
	const READING_ACCURACY_UNITS: Units = [
		['% of reading', '%'],
		['PPM of reading', 'p']
	];
	const RANGE_ACCURACY_UNITS: Units = [
		['Counts', 'c'],
		['% of range', '%'],
		['PPM of range', 'p']
	];
	const TOLERANCE_UNITS: Units = [
		['%', '%'],
		['PPM', 'p']
	];

	let resolutionValue = NaN;
	let resolutionUnit = 0;
	let rangeValue = NaN;
	let rangeUnit = 0;
	let readingAccuracyValue = NaN;
	let readingAccuracyUnit = 0;
	let rangeAccuracyValue = NaN;
	let rangeAccuracyUnit = 0;
	let referenceValue = NaN;
	let referenceUnit = 0;
	let toleranceValue = NaN;
	let toleranceUnit = 0;
	let measurementValue = NaN;
	let measurementUnit = 0;
	let resolution: number;
	let reference: number;
	let referenceError: number;
	let measurement: number;
	let measurementError: number;

	function multiply(value: number, unit: number) {
		return value * Math.pow(10, unit * 3);
	}

	$: {
		const range = multiply(rangeValue, rangeUnit);
		const digits = [Math.floor(Math.log10(resolutionValue) + 1), Math.ceil(resolutionValue)][resolutionUnit];
		resolution = Math.pow(10, Math.floor(Math.log10(range))) / Math.pow(10, digits - 1);
		reference = multiply(referenceValue, referenceUnit);
		referenceError = reference * (toleranceValue / [100, 1000000][toleranceUnit]);
		measurement = multiply(measurementValue, measurementUnit);
		measurementError = measurement * (readingAccuracyValue / [100, 1000000][readingAccuracyUnit]);
		measurementError += [
			resolution * rangeAccuracyValue,
			range * (rangeAccuracyValue / 100),
			range * (rangeAccuracyValue / 1000000)
		][rangeAccuracyUnit];
	}
</script>

<header>
	<Logo />
</header>
<main>
	<Section
		title="Multimeter Resolution"
		description="The smallest detectable change in a measurement, expressed as counts or digits. For example, a 6000 count multimeter has a 3.5 digit resolution."
	>
		<DimensionedInput units={RESOLUTION_UNITS} bind:value={resolutionValue} bind:unit={resolutionUnit} />
	</Section>
	<Section
		title="Multimeter Range"
		description="The span within which a measurement is made, indicating the maximum value measurable with specified precision."
	>
		<DimensionedInput units={RESISTANCE_UNITS} bind:value={rangeValue} bind:unit={rangeUnit} />
	</Section>
	<Section
		title="Measurement Accuracy"
		description="Indicates how close the measured value is to the true value, affected by the selected range and time since calibration."
	>
		<DimensionedInput
			units={READING_ACCURACY_UNITS}
			bind:value={readingAccuracyValue}
			bind:unit={readingAccuracyUnit}
		/>
		<span>+</span>
		<DimensionedInput units={RANGE_ACCURACY_UNITS} bind:value={rangeAccuracyValue} bind:unit={rangeAccuracyUnit} />
	</Section>
	<Section
		title="Reference Resistance"
		description="The actual resistor value, with uncertainty affected by temperature and time since calibration."
	>
		<DimensionedInput units={RESISTANCE_UNITS} bind:value={referenceValue} bind:unit={referenceUnit} />
		<span>±</span>
		<DimensionedInput units={TOLERANCE_UNITS} bind:value={toleranceValue} bind:unit={toleranceUnit} />
	</Section>
	<Section
		title="Measured Resistance"
		description="The resistance measured by the multimeter, which may differ from the true value."
	>
		<DimensionedInput units={RESISTANCE_UNITS} bind:value={measurementValue} bind:unit={measurementUnit} />
	</Section>
	<Result {resolution} {reference} {referenceError} {measurement} {measurementError} />
</main>
<footer>© {new Date().getFullYear()} Seven Crumbs</footer>

<style>
	main {
		display: flex;
		flex-direction: column;
		gap: calc(var(--unit) * 2);
		box-sizing: border-box;
		border: 1px solid var(--foreground-alternate);
		border-radius: var(--unit);
		padding: calc(var(--unit) * 4);
		width: 100%;
	}

	footer {
		color: var(--foreground-alternate);
	}
</style>
