<script>

    let numberOfPages = 0;
    let itemsPerPage = 10;
    let stringToFind = '';
    let users = [];

    /**
     * inits the component. First asks for the total number of entities to display
     * (endpoint needs to be implemented on API) and calculates the
     * number of pages. Then it loads the initial list of user
     * considering that client is on page 0.
     */
    function init() {
        const totalNumberOfItems = getTotalNumberOfItems();
        numberOfPages = Math.ceil(totalNumberOfItems / itemsPerPage);
        loadListOfUsers();
    }

    /**
     *  Gets the total number of entities so we can calculate the number of
     *  table pages.
     * TODO - Replace mocked answer once API implements endpoint. We should implement this
     * endpoint because on large table of data we can't ask for every item and calculate the
     * quantity of them on frontend.
     * @returns - total number of entities to request for
    */
    function getTotalNumberOfItems() {
        return 100;
    }

    /**
     *  Gets a list of users considering the current page and the number
     * of users to display per page
     * @params currentPage: number - current user page, if it is not provided a default value of 0 (first page) is assigned
     */
    function loadListOfUsers(currentPage = 0) {
        const offset = currentPage * itemsPerPage;
        let url =`https://hr.oat.taocloud.org/v1/users?limit=${itemsPerPage}&offset=${offset}`;
        if (stringToFind !== '') {
            url += `&name=${stringToFind}`;
        }
        console.log(url);
        fetch(url)
            .then((response) => {
                return response.json();
            })
            .then((listedUsers) => {
               users = [...listedUsers];
               console.log(users);
            });
    }

    /**
     * Gets a user information
     * TODO - Removed mocked user id
    */
    function getUser() {
        fetch('https://hr.oat.taocloud.org/v1/user/0')
            .then((response) => {
                return response.json();
            })
            .then((myJson) => {
                console.log(myJson);
            });
    }

 init();
</script>

<main>
	<button on:click={getUser}> test me!</button>
    <table>
        <thead>

            <th><input type='text' bind:value={stringToFind} on:input={() => {loadListOfUsers()}}></th>
            <th>First name</th>
            <th>Last name</th>
        </thead>
        <tbody>
            {#each users as user (user.userId)}
            <tr>
                <td>{user.userId + 1}</td>
                <td>{user.firstName}</td>
                <td>{user.lastName}</td>
            </tr>
            {/each}
        </tbody>
        <tfoot>
            {#each Array(numberOfPages) as _, pageIndex}
                <button on:click={() => {loadListOfUsers(pageIndex)}}>{pageIndex + 1}</button>
            {/each}
        </tfoot>
    </table>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

    table, tr, th {
        border: 1px solid black;
        border-collapse: collapse;
    }

    div.table-pagination {

    }

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
