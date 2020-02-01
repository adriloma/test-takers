<script>
    import Table from './components/table.svelte';
    import InfoBox from './components/info-box.svelte';

    let userToDisplayId = undefined;
</script>

<main>
    <Table on:display-user={(event) => userToDisplayId = event.detail.id }/>
    <InfoBox bind:userToDisplayId={userToDisplayId}/>
</main>

<style>
	main {
		padding: 1em;
		max-width: 240px;
        margin: 0 auto;
        display: flex;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
