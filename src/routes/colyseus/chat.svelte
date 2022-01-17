
<script>
	import { onMount, onDestroy } from 'svelte';
    import { Client } from 'colyseus.js';

	let client;
	let room;
    let message;
    let messageLog = [];

    onMount(async () => {
		client = new Client("ws://localhost:2567");

		room = await client.joinOrCreate("chat");
        console.log("joined");
        room.onStateChange.once(function(state) {
            console.log("initial room state:", state);
        });

        // new room state
        room.onStateChange(function(state) {
            // this signal is triggered on each patch
        });

        // listen to patches coming from the server
        room.onMessage("messages", function(message) {
            messageLog = [...messageLog, message];
        });

      });

    onDestroy(() => {
        if(client) {
        }
	});

    function handleSend() {
          room.send("message", message);
      }
</script>


  <p>This room doesn't use the room's state. It just broadcast messages through "broadcast" method.</p>
  <strong>Messages</strong><br>
  <input bind:value={message} placeholder="enter your message here">
  <button on:click={handleSend}>Send</button>

  <div id="messages">
	{#each messageLog as m}
		<p>{m}</p>
	{/each}

  </div>