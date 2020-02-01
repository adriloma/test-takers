<script>
import { writable } from 'svelte/store';

export let userToDisplayId = undefined;
 let userToDisplay = undefined;

$: {
            if (userToDisplayId !== undefined) {
                    getUser(userToDisplayId)
                    .then((user) => {
                        debugger;
                        userToDisplay = user;
                    });
            }
}
/**
 * Updates user to be displayed on the ser info box
 * @params id:number - id of the user to fetch
 **/
    export function updateUserToDisplay(id) {
        getUser(id)
            .then((user) => {
                debugger;
                userToDisplay = user;
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

</script>

<main>
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
</style>
