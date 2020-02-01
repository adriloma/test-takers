<script>

    let numberOfPages = 0;
    let itemsPerPage = 10;
    let stringToFind = '';
    let users = [];

    /**
     * inits the component loading the initial list of users
     */
    function init() {
        updateListOfUsers();
    }

    /**
     *  Gets a list of users considering the current page, the items per page, and the string to find for.
     * FIrst asks API how many users will be displayed to calculate pagination, and once the
     * number of pages has been calculated it asks for the current page users
     * @params currentPage: number - current user page; if it is not provided, a default value of 0 (first page) is assigned
     */
    function updateListOfUsers(currentPage = 0) {
        const totalNumberOfItems = getTotalNumberOfUsersToList()
            .then((totalNumberOfItems) => {
                numberOfPages = Math.ceil(totalNumberOfItems / itemsPerPage);
                const offset = currentPage * itemsPerPage;
                let url =`https://hr.oat.taocloud.org/v1/users?limit=${itemsPerPage}&offset=${offset}&name=${stringToFind}`;
                getUsers(url)
                    .then((listedUsers) => users = [...listedUsers]);

            })
    }

    /**
     *  Gets the total number of users that satisfy the filters.
     * FIXME - Currently the number of users to list is calcualted using the API endpoint to list the users (/users),  but with a mocked limit of 1000.
     *The endpoint must be replaced  once API implements a new endpoint to just get the number of coincidences. We should
     * implement this endpoint because on large table of data we can't ask for all coincidences and calculate
     * the quantity of them on frontend. Actually at this point is redundant to ask twice
     * for the list of users, but I leave it ready to just change the endpoint
     * once API implements its before next release.
     * @returns: promise - a promise with the total number after applying filters
    */
    function getTotalNumberOfUsersToList() {
        let url =`https://hr.oat.taocloud.org/v1/users?limit=1000&offset=0&name=${stringToFind}`;
        // TODO - This endpoint must be replaced with the new one!
        return getUsers(url)
            .then((listedUsers) => listedUsers.length);
    }

    /**
     * Request API a list of users
     * @returns : promise - promise containing the list of users
     */
    function getUsers(url) {
               return  fetch(url)
                    .then((response) => {
                        return response.json();
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

            <th><input type='text' bind:value={stringToFind} on:input={() => {updateListOfUsers()}}></th>
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
                <button on:click={() => {updateListOfUsers(pageIndex)}}>{pageIndex + 1}</button>
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
