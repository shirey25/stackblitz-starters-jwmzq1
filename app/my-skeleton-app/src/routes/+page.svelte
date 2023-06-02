<script lang="ts">
  import { localStorageStore } from "@skeletonlabs/skeleton";
  import { writable } from "svelte/store";

  interface Contact {
    name: string;
    email: string;
  }

  let inputName = "";
  let showEmailForm = false;
  let signUpEmail = "";
  let signUpPassword = "";

  function showEmailSignUpForm() {
    showEmailForm = true;
  }

  async function signUpWithEmail() {
    try {
      const { user, error } = await supabase.auth.signUp({
        email: signUpEmail,
        password: signUpPassword,
      });

      if (error) {
        console.error("Sign-up error:", error);
      } else {
        console.log("User signed up:", user);
        // Optionally, you can handle successful sign-up, such as showing a success message or redirecting to another page
      }
    } catch (error) {
      console.error("Sign-up error:", error);
    }
  }

  const contactStore = localStorageStore<Contact[]>("contactStore", []);

  function addContact() {
    const newContact: Contact = {
      name: inputName,
      email: signUpEmail,
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

  import { createClient } from "@supabase/supabase-js";
  import { supabaseUrl, supabaseKey } from "./config"; // Add your Supabase URL and Key in the config file

  const supabase = createClient(supabaseUrl, supabaseKey);

  const isLoggedIn = writable(false); // Store to track login status

  async function signInWithGoogle() {
    const { user, error } = await supabase.auth.signInWithProvider("google");
    if (error) {
      console.error("Login error:", error);
    } else {
      console.log("User logged in with Google:", user);
      isLoggedIn.set(true); // Update login status
      // Optionally, you can handle successful login, such as showing a success message or redirecting to another page
    }
  }

  function logout() {
    // Implement the logout logic here
    // Set isLoggedIn to false and clear any user data or session information
    isLoggedIn.set(false); // Update login status
  }
</script>

<div class="container h-full mx-auto flex justify-center items-center">
  <div class="space-y-5">
    <h1 class="h1">Login to Dash -GPT</h1>
    <p>This is an example of a login form with Svelte, Google Sign-In, and Email Sign-Up</p>

    {#if $isLoggedIn}
      <!-- User is logged in -->
      <div>
        <!-- Contact form -->
        <label class="label">
          <span>Name</span>
          <input class="input" type="text" placeholder="Name" bind:value="{inputName}" />

          <span>Email Address</span>
          <input class="input" type="email" placeholder="Email" bind:value="{signUpEmail}" />
        </label>

        <button type="button" class="btn variant-ghost" on:click="{addContact}">Add Contact</button>
        <hr/>

        <h2 class="h2">Your Previous Account Logins</h2>
        {#each $contactStore as contact, index}
          <div class="card p-2">
            <h3>{contact.name}</h3>
            <h3>{contact.email}</h3>
            <button type="button" class="btn variant-danger" on:click="{() => deleteContact(index)}">Delete</button>
          </div>
        {/each}

        <button type="button" class="btn variant-ghost" on:click="{logout}">Logout</button>
      </div>
    {:else}
      <!-- User is not logged in -->
      <div>
        <button type="button" class="btn variant-ghost" on:click="{signInWithGoogle}">Login with Google</button>
        <button type="button" class="btn variant-ghost" on:click="{showEmailSignUpForm}">Email Address</button>
      </div>

      {#if showEmailForm}
        <!-- Email sign-up form -->
        <div>
          <label class="label">
            <span>Email Address</span>
            <input class="input" type="email" placeholder="Email" bind:value="{signUpEmail}" />
          </label>

          <label class="label">
            <span>Password</span>
            <input class="input" type="password" placeholder="Password" bind:value="{signUpPassword}" />
          </label>

          <button type="button" class="btn variant-primary" on:click="{signUpWithEmail}">Sign Up</button>
        </div>
      {/if}
    {/if}
  </div>
</div>
