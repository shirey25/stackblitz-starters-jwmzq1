<script lang="ts">


  import { localStorageStore } from "@skeletonlabs/skeleton";
  import { writable } from "svelte/store";

  interface Contact {
    name: string;
    phoneNumber: string;
  }

  const client = new Client();

  client
    .setEndpoint('https://ilokcuwsofdogojuvlln.supabase.co') // Your API Endpoint
    .setProject('eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imlsb2tjdXdzb2Zkb2dvanV2bGxuIiwicm9sZSI6ImFub24iLCJpYXQiOjE2Nzc5ODY0NDksImV4cCI6MTk5MzU2MjQ0OX0.xYz0J-4M0hVyymwyYKaAMofXxz_i9PX-HCrJrVA6OwY); // Your project ID

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
    inputEmail = "";
  }

  async function loginWithGoogle() {
    try {
      // Redirect to Google authentication
      await client.account.createOAuth2Session("google");

  async function signInWithGoogle() {
  const { data, error } = await supabase.auth.signInWithOAuth({
    provider: 'google',
  })
}

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

      <span>Email</span>
      <input class="input" type="Email" placeholder="example@example.com" bind:value="{inputEmail" />
    </label>
<!-- Login form -->
    <div>
      <button type="button" class="btn variant-ghost" on:click="{signInWithGoogle}">Login with Google</button>
    </div>
    <button type="button" class="btn variant-ghost" on:click="{addContact}">Add Contact</button>
    <hr />

    <h2 class="h2">Your Previous Acct Logins /h2>
    {#each $contactStore as contact, index}
    <div class="card p-2">
      <h3>{contact.name}</h3>
      <h3>{contact.phoneNumber}</h3>
      <button type="button" class="btn variant-danger" on:click="{() => deleteContact(index)}">Delete</button>
    </div>
    {/each}

    <button type="button" class="btn variant-ghost" on:click="{logout}">Logout</button>
    {:else}
 
   {#if isLoggedIn}
    <!-- Contact form -->
    <label class="label">
      <span>Name</span>
      <input class="input" type="text" placeholder="Name" bind:value="{inputName}" />

      <span>Phone Number</span>
      <input class="input" type="text" placeholder="111-111-1111" bind:value="{inputPhoneNumber}" />
    </label>
    {/if}
  </div>
</div>