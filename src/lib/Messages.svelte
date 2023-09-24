<script lang="ts">
    import { onMount, onDestroy } from 'svelte';
    import { currentUser, pb } from './pocketbase';
    import { Record } from 'pocketbase';

    const importData = [
      {
        "id": "748ccw3vczzpbfd",
        "name": "messages",
        "type": "base",
        "system": false,
        "schema": [
            {
                "id": "qukooojo",
                "name": "text",
                "type": "text",
                "system": false,
                "required": true,
                "options": {
                    "min": 1,
                    "max": null,
                    "pattern": ""
                }
            }
        ],
        "indexes": [],
        "listRule": "",
        "viewRule": "",
        "createRule": "",
        "updateRule": null,
        "deleteRule": null,
        "options": {}
      }
    ];
    let newMessage: string;
    let messages = [];
    let unsubscribe: () => void;

    onMount(async () => {
      await pb.admins.authWithPassword('admin@admin.com', 'admin@12345678');
      await pb.collections.import(importData, false);

      let resultList = await pb.collection('messages').getList(1, 8, {
          sort: '-created',
        });
      messages = resultList.items.reverse();
      unsubscribe = await pb
        .collection('messages')
        .subscribe('*', async ({ action, record }) => {
          if (action === 'create') {
            if (messages.length >= 8) {
              messages.shift()
            }
            messages = [...messages, record];
          }
          if (action === 'delete') {
            messages = messages.filter((m) => m.id !== record.id);
          }
        });
    });
  
    onDestroy(() => {
      unsubscribe?.();
    });
  
    async function sendMessage() {
      const data = {
        text: newMessage,
      };
      const createdMessage = await pb.collection('messages').create(data);
      newMessage = '';
    }
  </script>

<div class="sm:text-center ">
  <div class="messages flex flex-col ">
      <table class="table-auto sm:text-center">
        <thead>
          <tr>
            <th></th>
            <th></th>
          </tr>
        </thead>
        {#each messages as message (message.id)}
        <tbody>
          <tr>
            <td><img class="avatar" src={`https://avatars.dicebear.com/api/identicon/null.svg`}
              alt="avatar"
              width="60px"/></td>
            <td><p class="px-8 msg-text text-white break-words text-sm max-w-prose">{message.text}</p></td>
          </tr>
        </tbody>
        <br>
        {/each}
      </table>
      <div class="submit">
        <form on:submit|preventDefault={sendMessage}>
          <input type="text" id="large-input" bind:value={newMessage} placeholder="Message"
                       class="block w-full p-6 text-gray-900 border border-gray-300 rounded-lg bg-gray-50 sm:text-md focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
                       ><br>
          <button type="submit" class="px-8 border-white text-white">Send</button>
        </form>
      </div> 
  </div>
</div>
