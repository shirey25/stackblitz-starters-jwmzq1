<script lang="ts">
	import { localStorageStore } from "@skeletonlabs/skeleton" // Backs up our data
	import type { Writable } from "svelte/store" // Handles our data

	// Writable
	// Read / Write from throughout our application
	// Writable needs a type
	/*  Writable 
		[
			{ name: "Cooper", phoneNumber: "(123) 123 - 1234" }
		]
	*/

	let inputName = "";
	let inputPhoneNumber = "";

	interface Contact {
		name: string
		phoneNumber: string
	}

	// "contacts" = Contact[]
	const contactStore: Writable<Contact[]> = localStorageStore("contactStore", []);

	// contactStore is a writable which holds an array of contacts
	// $contactStore 
	// contactStore is the writable object (subscribe, delete, change value)
	// $contactStore is how you get the VALUE of your store

	function addContact() {
		// $contactStore = [ cooper, peter ]   ...[cooper, peter] = cooper, peter
		// [bob, ...[cooper, peter] ] = [bob, cooper, peter]

		$contactStore = [
			{
				name: inputName,
				phoneNumber: inputPhoneNumber
			},
			...$contactStore
		]
	}

	function deleteContact(index: number) {
		// [bob, cooper, peter]
		// 1
		// bob 0 !== 1 TRUE
		// cooper 1 !== 1 FALSE
		// peter 2 !== 1 TRUE

		// $contactStore = [bob, peter]

		$contactStore = $contactStore.filter((contact, contactIndex) => contactIndex !== index);
	}

</script>

<div class="container h-full mx-auto flex justify-center items-center">
	<div class="space-y-5">
		<h1 class="h1">ContactList</h1>
		<p>All your contacts in one persistent state!</p>
		<label class="label">
			<span>Name</span>
			<input class="input" type="text" placeholder="Name" bind:value={inputName} />
		</label>
		<label class="label">
			<span>Phone Number</span>
			<input class="input" type="text" placeholder="Phone Number" bind:value={inputPhoneNumber} />
		</label>
		<button type="button" class="btn variant-ghost" on:click={addContact}>Add Contact</button>
		<hr />
		<h2 class="h2">Your Contacts</h2>
		{#each $contactStore as contact, index }
			<div class="card p-2">
				<h1>{contact.name}</h1>
				<h1>{contact.phoneNumber}</h1>
				<button type="button" class="btn btn-sm variant-filled-error" on:click={() => deleteContact(index)}>Delete</button>
			</div>
		{/each}
	</div>
</div>