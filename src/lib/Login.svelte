<!-- <script lang="ts">
    import { currentUser, pb } from './pocketbase';
    import Messages from "./Messages.svelte";
  
    let username: string;
    let password: string;
  
    async function login() {
      const user = await pb.collection('users').authWithPassword(username, password);
      console.log(user)
    }
  
    async function signUp() {
      try {
        const data = {
          username,
          password,
          passwordConfirm: password,
          name: null,
        };
        const createdUser = await pb.collection('users').create(data);
        await login();
      } catch (err) {
        console.error(err)
      }
    }
  
    function signOut() {
      pb.authStore.clear();
    }
  
  </script>
  
  {#if $currentUser}
    <div class="md:container md:mx-auto">
        <div class="flex space-x-4">
            <h1 class="text-white px-14">PocketChat</h1>
            <button on:click={signOut} class="text-black">Sign Out</button>
        </div><br><hr><br>
    
        <div>
            <Messages />
        </div>
    </div>

  {:else}
    <h1 class="text-white">PocketChat</h1><br>
    <form on:submit|preventDefault>
      <input
        placeholder="Username"
        type="text"
        bind:value={username}
      />
  
      <input
        placeholder="Password" 
        type="password" 
        bind:value={password} 
      />
      <button on:click={signUp}>Sign Up</button>
      <button on:click={login}>Login</button>
    </form>
  {/if} -->

<script lang="ts">
  import Messages from "./Messages.svelte";
</script>

<div class="md:container md:mx-auto">
  <div class="flex space-x-4">
      <h1 class="text-white px-14">PocketChat</h1>
  </div><br><hr><br>

  <div>
      <Messages />
  </div>
</div>