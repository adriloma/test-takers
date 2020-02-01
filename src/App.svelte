<script>
import Table from './components/table.svelte';
import InfoBox, { updateUserToDisplay } from './components/info-box.svelte';

function test(params) {
    updateUserToDisplay(params.detail.id);
}
</script>

<main>
    <Table on:display-user={test}/>
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
