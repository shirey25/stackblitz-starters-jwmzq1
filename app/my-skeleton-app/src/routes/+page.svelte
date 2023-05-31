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


const sdk = require ('appwrite');
let client = new sdk.Client();
client
    setEndpoint('https://cloud.appwrite.io/v1'); // Your API Endpoint
    setProject('64749decd0dbb1e27c93');
    setKey('737d67b239611e525f7ff57d2d17d95b5310906971643bbfc564f223eb705434f6884f96fe4cbdcf1a79efcf2cbbd90365fd9a1a497ab0cd1220756702ae4db1cdac0731c57ccff2d7889c3e44030355bb3f6ec19be9cb7e27c92e142c5ec3e078f47f256493ca45ffb0f2d2598789875a74c76f4abc07679309482fb901d898')               // Your project ID



   


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