<script lang="ts">
  import { Client, Account, ID } from "appwrite";
  import { localStorageStore } from "@skeletonlabs/skeleton";
  import { writable } from "svelte/store";

  interface Contact {
    name: string;
    phoneNumber: string;
  }

  let inputName = "";
	let email = "";
  let inputPhoneNumber = "";
  let isLoggedIn = false;
	let password = "";


const client = new Client()
    .setEndpoint('https://cloud.appwrite.io/v1') // Your API Endpoint
    .setProject('64749decd0dbb1e27c93');               // Your project ID

const account = new Account(client);

   
);

promise.then(function (response) {
    console.log(response);
}, function (error) {
    console.log(error);
});
  

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

  async function login() {
    try {
      await appwrite.account.createSession(
        "EMAIL",
        "YOUR_EMAIL",
        "YOUR_PASSWORD"
      );
      isLoggedIn = true;
    } catch (error) {
      console.error("Login error:", error);
    }
  }

  function logout() {
    appwrite.account.deleteSession("current");
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
        <label class="label">
          <span>Email</span>
          <input class="input" type="email" placeholder="Email" bind:value="{email}" />
        </label>

        <label class="label">
          <span>Password</span>
          <input class="input" type="password" placeholder="Password" bind:value="{password}" />
        </label>

        <button type="button" class="btn variant-primary" on:click="{login}">Login</button>
      </div>
    {/if}
  </div>
</div>