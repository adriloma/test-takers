<script>

    function init() {
        fetch('https://hr.oat.taocloud.org/v1/users?limit=2000&offset=0')
            .then((response) => {
                return response.json();
            })
            .then((myJson) => {
                console.log(myJson);
            });
    }
    init();
    function getUser() {
        fetch('https://hr.oat.taocloud.org/v1/user/0')
            .then((response) => {
                return response.json();
            })
            .then((myJson) => {
                console.log(myJson);
            });
    }
</script>

<main>
	<button on:click={getUser}> test me!</button>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
