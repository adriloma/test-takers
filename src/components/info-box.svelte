<script>
import { writable } from 'svelte/store';

export let userToDisplayId = undefined;
let userToDisplay = undefined;
$:  updateUserToDisplay(userToDisplayId);

/**
 * Updates user to be displayed on the  info box
 * @params id:number - id of the user to fetch
 **/
    export function updateUserToDisplay(id) {
        if (id === undefined) {
            return;
        }
        getUser(id)
            .then((user) => {
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
        userToDisplayId = undefined;
    }
</script>

<div class='container'>
    {#if userToDisplay !== undefined}
        <div class='user-info-box'>
            <img src="{userToDisplay.picture}" alt="{userToDisplay.firstName + ' ' + userToDisplay.lastName}">
            <div>
                <p><strong>{`${userToDisplay.title } ${userToDisplay.firstName} ${userToDisplay.lastName}` }</strong></p>
                <p><strong>email: </strong>{userToDisplay.email}</p>
                <p><strong>address: </strong>{userToDisplay.address}</p>
            </div>
            <a on:click={unsetUserToDisplay}>&#10006;</a>
        </div>
    {/if}
</div>

<style>
    .container {
        margin-left: auto;
        margin-top: 40px;
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

    .user-info-box a {
        text-decoration: none;
        cursor: pointer;
        color: #333;
    }
</style>
