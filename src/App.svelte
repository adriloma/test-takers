<script>
    let users = [];
    function init() {
        fetch('https://hr.oat.taocloud.org/v1/users?limit=20&offset=0')
            .then((response) => {
                return response.json();
            })
            .then((listedUsers) => {
               users = [...listedUsers]
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
    <table>
        <thead>
            <th>-</th>
            <th>First name</th>
            <th>Last name</th>
        </thead>
        <tbody>
            {#each users as user}
            <tr>
                <td>{user.userId + 1}</td>
                <td>{user.firstName}</td>
                <td>{user.lastName}</td>
            </tr>
            {/each}
        </tbody>
    </table>
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

    table, tr, th {
        border: 1px solid black;
        border-collapse: collapse;
    }

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
