<script>
	import { onMount } from 'svelte';
	import BackgroundLoader from '../components/BackgroundLoader.svelte';
	import AComponent from '../components/AComponent.svelte';
	import AnotherComponent from '../components/AnotherComponent.svelte';

	let component;

	onMount( () => {
		component = AComponent;
	})
</script>

<svelte:head>
	<title>Background loading test</title>
</svelte:head>

<p>The images inside the components have 200 height on screen but actually quite large, more than 1000x1000, so they should take a little while to load.</p>
<button on:click={() => {component = AnotherComponent}}>Load something else</button>
<BackgroundLoader {component}>
<p>Place holder...</p>
</BackgroundLoader>