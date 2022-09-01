<script lang="ts" context="module">
import { writable } from "svelte/store";

let touchedKeyElements = writable(new Set<HTMLElement>());

// Seems inefficient to check this every time, but this does not appear to be a bottleneck
const updateTouchedKeys = (event: TouchEvent) => {
    touchedKeyElements.set(new Set([...event.touches]
            .map(touch => document.elementFromPoint(touch.clientX, touch.clientY))
            .filter(element => element?.tagName.toLowerCase() === "key-") as HTMLElement[]));
};
</script>

<svelte:window on:touchstart={updateTouchedKeys}
        on:touchmove={updateTouchedKeys}
        on:touchend={updateTouchedKeys} />

<script lang="ts">
import { getContext, onMount } from "svelte";
// import { createEventDispatcher } from "svelte";

import { key } from "./keyboardContext";

/* const dispatch = createEventDispatcher<{
    down: string,
}>(); */

const {
    includeCompoundKeys,
    keysInStroke,
    active,
} = getContext(key);

export let label: string = "";
export let values: string[] = [label];

let cls: string = "";
export { cls as class };

let keyElement: HTMLElement;
$: touched = $active && $touchedKeyElements.has(keyElement);
$: pressed = touched || values.every(key => $keysInStroke.get(key));
</script>

<key- class={cls}
        class:pressed
        class:touched
        data-value={values}
        bind:this={keyElement}>{label}</key->

<style lang="scss">
key-.number {
    grid-column: 1 / -1;
}
// key-.s,
// key-.asterisk {
//     grid-row: span 2;
// }

// .has-compound-keys {
    key-.s,
    key-.asterisk {
        grid-row: span 3;
    }
// }

key- {
    border-radius: 8px;
    border: 1px solid #aaa;
    // padding: 0.6em 1.2em;
    font-size: 1em;
    font-weight: 500;
    font-family: inherit;
    background-color: #1a1a1a;
    // cursor: pointer;
    // transition: border-color 0.25s;

    display: grid;
    place-items: center;

    // min-width: var(--key-size);

    &.pressed {
        background: #686fff;
        color: #fff;
        border-color: #11a;
    }

    &.touched {
        background: #382fef;

        // animation: pulse 0.125s forwards;
    }

    // @keyframes pulse {
    //     0% {
    //         background: #a1a7ff;
    //     }

    //     100% {
    //         background: #aaafff;
    //     }
    // }
}
key-:focus,
key-:focus-visible {
    outline: 4px auto -webkit-focus-ring-color;
}

@media (prefers-color-scheme: light) {
    key- {
        background-color: #f9f9f9;
    }
}
</style>