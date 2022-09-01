<script lang="ts">
import Keyboard from "./lib/Keyboard.svelte";

let lastStroke = "…";
</script>


<svelte:window on:touchstart|preventDefault />

<svelte:head>
    <title>On-screen stenotype</title>
</svelte:head>

<main>
    <div>{lastStroke}</div>
    <Keyboard on:stroke={event => { lastStroke = event.detail; }} />

    <p><b>On Windows 11:</b> This works best after disabling 3- and 4-finger touch gestures in Settings (<a href="ms-settings:devices-touch"><code>Bluetooth & devices</code> > <code>Touch</code></a>)</p>
</main>

<style>
main {
    display: flex;
    flex-flow: column;
    gap: 4em;
}

div {
    font-size: 4em;
    white-space: nowrap;
}
</style>