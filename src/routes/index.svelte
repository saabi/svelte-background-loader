<script>
	import { onMount } from 'svelte';
	import BackgroundLoader from '../components/BackgroundLoader.svelte';
	import AComponent from '../components/AComponent.svelte';

	let component;

	onMount( () => {
		component = AComponent;
	})
</script>

<svelte:head>
	<title>Background loading test</title>
</svelte:head>

<BackgroundLoader {component}>
<p>Place holder...</p>
</BackgroundLoader>