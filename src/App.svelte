<script>
import Table from './components/table.svelte';
import InfoBox from './components/info-box.svelte';
</script>

<main>
    <Table/>
    <InfoBox/>
</main>

<style>
	main {
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
