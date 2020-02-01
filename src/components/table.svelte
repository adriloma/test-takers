<script>
    import { onMount, createEventDispatcher } from 'svelte';

    const dispatch = createEventDispatcher();
    let numberOfPages = 0;
    let itemsPerPage = 10;
    let stringToFind = '';
    let users = [];

    /**
     * Initialize user list when component inits
     */
    onMount(async () => {
        updateListOfUsers();
    })

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
     * once API implements it before next release.
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
     * filtering is done on server side so In order to avoid a API request on each key press
     * a timeout of500ms is set befure updating list of users
    */
    function filtersUpdated() {
        setTimeout(() => updateListOfUsers(), 500);
    }

    /**
     * Dispatch an event to notify that a table element has been selected
     */
    function dispatchUserSelection(id) {
        dispatch('display-user', {id: id})
    }
</script>
<div class='container'>
    <input type='text' placeholder='search on table...' title='Type something' bind:value={stringToFind} on:input={() => {filtersUpdated()}}>
    <select bind:value={itemsPerPage} on:change={() => {updateListOfUsers()}}>
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
            {#each users  as user (user.userId)}
            <tr on:click={() => dispatchUserSelection(user.userId)}>
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
</div>
<style>

    .container {
        width: 60%;
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
</style>
