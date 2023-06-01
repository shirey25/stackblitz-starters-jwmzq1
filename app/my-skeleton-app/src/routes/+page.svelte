<script lang="ts">
  import { Client } from "appwrite";
  import { localStorageStore } from "@skeletonlabs/skeleton";
  import { writable } from "svelte/store";

  interface Contact {
    name: string;
    phoneNumber: string;
  }

  const client = new Client();

  client
    .setEndpoint('https://cloud.appwrite.io/v1') // Your API Endpoint
    .setProject('64749decd0dbb1e27c93'); // Your project ID

  let inputName = "";
  let inputPhoneNumber = "";
  let isLoggedIn = false;
  let email = "";
  let password = "";

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

  async function loginWithGoogle() {
    try {
      // Redirect to Google authentication
      await client.account.createOAuth2Session("google");

      // Once the user is redirected back to your app, you can check if the session was created
      const response = await client.account.get();

      if (response.$id) {
        isLoggedIn = true;
      }
    } catch (error) {
      console.error("Login error:", error);
    }
  }

  function logout() {
    client.account.deleteSession("current");
    isLoggedIn = false;
  }
</script>

<div class="container h-full mx-auto flex justify-center items-center">
  <div class="space-y-5">
    <h1 class="h1">Contact List</h1>
    <p>This is an example of a login form with Svelte and Appwrite</p>

    {#if isLoggedIn}
    <!-- Contact form -->
    <label class="label">
      <span>Name</span>
      <input class="input" type="text" placeholder="Name" bind:value="{inputName}" />

      <span>Phone Number</span>
      <input class="input" type="text" placeholder="111-111-1111" bind:value="{inputPhoneNumber}" />
    </label>

    <button type="button" class="btn variant-ghost" on:click="{addContact}">Add Contact</button>
    <hr />

    <h2 class="h2">Your Contact List</h2>
    {#each $contactStore as contact, index}
    <div class="card p-2">
      <h3>{contact.name}</h3>
      <h3>{contact.phoneNumber}</h3>
      <button type="button" class="btn variant-danger" on:click="{() => deleteContact(index)}">Delete</button>
    </div>
    {/each}

    <button type="button" class="btn variant-ghost" on:click="{logout}">Logout</button>
    {:else}
    <!-- Login form -->
    <div>
      <button type="button" class="btn variant-primary" on:click="{loginWithGoogle}">Login with Google</button>
    </div>
    {/if}
  </div>
</div>