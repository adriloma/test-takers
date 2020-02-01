<script>

    let numberOfPages = 0;
    let itemsPerPage = 10;
    let stringToFind = '';
    let users = [];
    let userToDisplay = undefined;

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
     * As filtering is done on serverside,  In order to avoid a API request on each key press,
     * a timeout of500ms is set befure updating list of users
    */
    function filtersUpdated() {
        setTimeout(() => updateListOfUsers(), 500);
    }

/**
 * Updates user to be displayed on the ser info box
 * @params id:number - id of the user to fetch
 **/
    function updateUserToDisplay(id) {
        getUser(id)
            .then((user) => {
                userToDisplay = user;
                console.log(userToDisplay);
            });
    }
    /**
     * Gets a user information
     * @params id: number - id of the user to fetch
     * @returns promise - promise containing the feteched user info
    */
    function getUser(id) {
        return fetch(`https://hr.oat.taocloud.org/v1/user/${id}`)
            .then((response) => {
                return response.json();
            });
    }

    /**
     * Cleans current uset to display and sets it to undefined
     */
    function unsetUserToDisplay() {
        userToDisplay = undefined;
    }

 init();
</script>

<main>
    <input type='text' placeholder='search on table...' title='Type something' bind:value={stringToFind} on:input={() => {filtersUpdated()}}>
    <select bind:value={itemsPerPage} on:change={() => {updateListOfUsers()}}>
        <option value='05'>05</option>
        <option value='10' selected>10</option>
        <option value='20'>20</option>
        <option value='30'>30</option>
        <option value='50'>50</option>
    </select>
    <table>
        <thead>
            <th>Id</th>
            <th>First name</th>
            <th>Last name</th>
        </thead>
        <tbody>
            {#each users as user (user.userId)}
            <tr on:click={() => updateUserToDisplay(user.userId)}>
                <td>{user.userId}</td>
                <td>{user.firstName}</td>
                <td>{user.lastName}</td>
            </tr>
            {/each}
        </tbody>
    </table>
    {#if numberOfPages > 1}
        <ul class='pagination'>
            {#each Array(numberOfPages) as _, pageIndex}
                <li>
                    <a on:click={() => {updateListOfUsers(pageIndex)}}>{pageIndex + 1}</a>
                </li>
            {/each}
        </ul>
    {/if}

    {#if userToDisplay !== undefined}
        <div class='user-info-box'>
            <img src="{userToDisplay.picture}" alt="{userToDisplay.firstName + ' ' + userToDisplay.lastName}">
            <div>
                <p><strong>{`${userToDisplay.title } ${userToDisplay.firstName} ${userToDisplay.lastName}` }</strong></p>
                <p><strong>email: </strong>{userToDisplay.email}</p>
                <p><strong>address: </strong>{userToDisplay.address}</p>
            </div>
            <button on:click={unsetUserToDisplay}>close</button>
        </div>
    {/if}
</main>

<style>
	main {
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

    table {
        text-align: center;
        width: 100%;
        border-collapse: collapse
    }

    thead {
        background: #2e2759;
        color: #fff;
    }
    table, tr, th {
        border: 1px solid black;
    }

    tr, th, td {
        padding: 12px
    }

    tr {
        cursor:  pointer;
    }

    tr:nth-child(odd) {
        background: whitesmoke
    }

    tr:nth-child(even) {
        background: lightgray
    }

    tr:hover {
        background: lightyellow
    }
    .pagination {
        padding: 0px
    }
    .pagination li {
        display: inline-block;
    }
    .pagination li a {
        text-decoration: none;
        cursor: pointer;
        color: #333;
        padding: 8px 16px;
    }

    .pagination li a:hover {
        background: #ddd
    }

    .user-info-box {
        border: 1px solid black;
        border-radius: 5px;
        background: lightyellow;
        display: flex;
    }

    .user-info-box img{
        margin-right: 25px;
        height: 100px;
        border-right: 1px solid black;
        border-bottom: 1px solid black;
    }

    .user-info-box button {
        align-self: end;
        margin-left: auto;
    }

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
