<script lang="ts">

  import { localStorageStore } from "@skeletonlabs/skeleton";
  import { writable } from "svelte/store";

  interface Contact {
    name: string;
    phoneNumber: string;
  }

  let inputName = "";
  let inputPhoneNumber = "";
  let isLoggedIn = false;

  const contactStore = localStorageStore<Contact[]>("contactStore", []);

  function addContact() {
    const newContact: Contact = {
      name: inputName,
      phoneNumber: inputPhoneNumber,
    };

    contactStore.set([newContact, ...contactStore.get()]);
    clearInput();
  }

  function deleteContact(index: number) {
    const contacts = contactStore.get().filter((_, i) => i !== index);
    contactStore.set(contacts);
  }

  function clearInput() {
    inputName = "";
    inputPhoneNumber = "";
  }

  
  
  async function signInWithGoogle() {
    const { data, error } = await supabase.auth.signInWithOAuth({
    provider: 'google',
  })
      // Implement the Google Sign-In logic here
      // You can use the Google Sign-In API or any other method of your choice
      // Set isLoggedIn to true upon successful sign-in
  }

  function logout() {
    // Implement the logout logic here
    // Set isLoggedIn to false and clear any user data or session information
  }
</script>

<div class="container h-full mx-auto flex justify-center items-center">
  <div class="space-y-5">
    <h1 class="h1">Login to Dash -GPT</h1>
    <p>This is an example of a login form with Svelte and Google Sign-In</p>

    {#if isLoggedIn}
      <!-- User is logged in -->
      <div>
        <!-- Contact form -->
        <label class="label">
          <span>Name</span>
          <input class="input" type="text" placeholder="Name" bind:value="{inputName}" />

          <span>Phone Number</span>
          <input class="input" type="text" placeholder="111-111-1111" bind:value="{inputPhoneNumber}" />
        </label>

        <button type="button" class="btn variant-ghost" on:click="{addContact}">Add Contact</button>
        <hr/>

        <h2 class="h2">Your Previous Account Logins</h2>
        {#each $contactStore as contact, index}
          <div class="card p-2">
            <h3>{contact.name}</h3>
            <h3>{contact.phoneNumber}</h3>
            <button type="button" class="btn variant-danger" on:click="{() => deleteContact(index)}">Delete</button>
          </div>
        {/each}

        <button type="button" class="btn variant-ghost" on:click="{logout}">Logout</button>
      </div>
    {:else}
      <!-- User is not logged in -->
      <div>
        <button type="button" class="btn variant-ghost" on:click="{signInWithGoogle}">Login with Google</button>
      </div>
    {/if}
  </div>
</div>
