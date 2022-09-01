<script lang="ts">
import { createEventDispatcher, setContext } from "svelte";
import { writable } from "svelte/store";

import { key } from "./keyboardContext";
import Key from "./Key.svelte";

const dispatch = createEventDispatcher<{
    stroke: string,
}>();

let includeCompoundKeys = true;

const keyOrder = [
    "#",
    "S",
    "T",
    "K",
    "P",
    "W",
    "H",
    "R",
    "A",
    "O",
    "*",
    "E",
    "U",
    "-F",
    "-R",
    "-P",
    "-B",
    "-L",
    "-G",
    "-T",
    "-S",
    "-D",
    "-Z",
];

const keysInStroke = writable(new Map<string, boolean>(keyOrder.map(key => [key, false])));
$: hyphenated = ![..."AO*EU"].some(char => $keysInStroke.get(char));
/* $: stroke = [...keysInStroke]
        .filter(([key, pressed]) => pressed)
        .map(([key]) => key.replace(/-/g, ""))
        .join(""); */

$: stroke = (() => {
    let stroke = "";
    let hyphenChecked = false;
    for (const [key, pressed] of $keysInStroke) {
        if (!pressed) continue;

        if (!hyphenChecked && key.includes("-") && hyphenated) {
            stroke += "-";
            hyphenChecked = true;
        }

        stroke += key.replace(/-/g, "");
    }
    return stroke || "";
})();

const resetKeys = () => {
    for (const key of $keysInStroke.keys()) {
        $keysInStroke.set(key, false);
    }
};


let nTouches = 0;
let active = writable(false);
$: $active = nTouches !== 0;
const onTouchUpdate = (event: TouchEvent) => {
    // console.log(event);
    nTouches = event.touches.length;

    const keys = [...event.changedTouches]
            .map(touch =>
                    document.elementFromPoint(touch.clientX, touch.clientY)
                            ?.getAttribute("data-value")
                            ?.split(",")
                    ?? []   
            )
            .flat();

    keys.forEach(key => {
        $keysInStroke.set(key, true);
    });
    $keysInStroke = $keysInStroke;
};

const onTouchEnd = (event: TouchEvent) => {
    nTouches = event.touches.length;

    if (nTouches === 0 && stroke) {
        dispatch("stroke", stroke);

        resetKeys();
        $keysInStroke = $keysInStroke;
    }
};


setContext(key, {
    includeCompoundKeys,
    keysInStroke,
    active,
});
</script>

<keyboard- on:touchstart|preventDefault={onTouchUpdate}
        on:touchmove={onTouchUpdate}
        on:touchend={onTouchEnd}
        class:has-compound-keys={includeCompoundKeys}>
    <div class="main-rows">
        <Key label="#"
                class="number" />

        {#if includeCompoundKeys}
            <Key values={["#", "S"]} />
            <Key values={["#", "T"]} />
            <Key values={["#", "P"]} />
            <Key values={["#", "H"]} />
            <Key values={["#", "H", "*"]} />
            <Key values={["#", "*"]} />
            <Key values={["#", "*", "-F"]} />
            <Key values={["#", "-F"]} />
            <Key values={["#", "-P"]} />
            <Key values={["#", "-L"]} />
            <Key values={["#", "-T"]} />
            <Key values={["#", "-T", "-D"]} />
            <Key values={["#", "-D"]} />
        {/if}
        
        <Key label="S"
                class="s" />
        <Key label="T" />
        <Key label="P" />
        <Key label="H" />
        {#if includeCompoundKeys}
            <Key values={["H", "*"]} />
        {/if}
        <Key label="*"
                class="asterisk" />
        {#if includeCompoundKeys}
            <Key values={["*", "-F"]} />
        {/if}
        <Key label="F" values={["-F"]} />
        <Key label="P" values={["-P"]} />
        <Key label="L" values={["-L"]} />
        <Key label="T" values={["-T"]} />
        <Key values={["-T", "-D"]} />
        <Key label="D" values={["-D"]} />

        
        {#if includeCompoundKeys}
            <Key values={["T", "K"]} />
            <Key values={["P", "W"]} />
            <Key values={["H", "R"]} />
            <Key values={["H", "R", "*"]} />

            <Key values={["*", "-F", "-R"]} />
            <Key values={["-F", "-R"]} />
            <Key values={["-P", "-B"]} />
            <Key values={["-L", "-G"]} />
            <Key values={["-T", "-S"]} />
            <Key values={["-T", "-S", "-D", "-Z"]} />
            <Key values={["-D", "-Z"]} />
        {/if}

        <Key label="K" />
        <Key label="W" />
        <Key label="R" />
        {#if includeCompoundKeys}
            <Key values={["R", "*"]} />
        {/if}

        {#if includeCompoundKeys}
            <Key values={["*", "-R"]} />
        {/if}
        <Key label="R" values={["-R"]} />
        <Key label="B" values={["-B"]} />
        <Key label="G" values={["-G"]} />
        <Key label="S" values={["-S"]} />
        {#if includeCompoundKeys}
            <Key values={["-S", "-Z"]} />
        {/if}
        <Key label="Z" values={["-Z"]} />

        <!--
            <Key label="S"
                    class="s" />
            <Key label="T" />
            <Key label="K" />
            <Key label="P" />
            <Key label="W" />
            <Key label="H" />
            <Key label="R" />
            <Key label="*"
                    class="asterisk" />
            <Key label="F" values={["-F"]} />
            <Key label="R" values={["-R"]} />
            <Key label="P" values={["-P"]} />
            <Key label="B" values={["-B"]} />
            <Key label="L" values={["-L"]} />
            <Key label="G" values={["-G"]} />
            <Key label="T" values={["-T"]} />
            <Key label="S" values={["-S"]} />
            <Key label="D" values={["-D"]} />
            <Key label="Z" values={["-Z"]} />
        -->
    </div>

    <div class="vowel-row">
        <div>
            <Key label="A" />
            {#if includeCompoundKeys}
                <Key values={["A", "O"]} />
            {/if}
            <Key label="O" />
        </div>

        <div>
            <Key label="E" />
            {#if includeCompoundKeys}
                <Key values={["E", "U"]} />
            {/if}
            <Key label="U" />
        </div>
    </div>
</keyboard->

<!-- <button on:click={increment}>
  count is {count}
</button> -->

<style lang="scss">
keyboard- {
    --key-size: 2.75cm;
    --key-size-compound: 1.25cm;
    --key-size-reduced: calc(var(--key-size) - var(--key-size-compound) / 2);

    display: flex;
    flex-direction: column;
    gap: 3cm;

    user-select: none;

    .main-rows {
        display: grid;
        grid-template-columns: repeat(4, var(--key-size)) calc(2 * var(--key-size))
                repeat(5, var(--key-size));
        grid-template-rows: repeat(3, var(--key-size));

        // grid-auto-flow: column;
    }

    &.has-compound-keys .main-rows {
        grid-template-columns:
                repeat(3, var(--key-size))
                var(--key-size-reduced)
                var(--key-size-compound)
                calc(2 * var(--key-size-reduced))
                var(--key-size-compound)
                var(--key-size-reduced)
                repeat(2, var(--key-size))
                var(--key-size-reduced)
                var(--key-size-compound)
                var(--key-size-reduced);
        grid-template-rows:
                calc(var(--key-size-reduced) * 0.75)
                repeat(2,
                    var(--key-size-compound)
                    var(--key-size-reduced)
                );
    }

    .vowel-row {
        display: flex;
        height: var(--key-size);
        justify-content: center;
        padding-right: var(--key-size);

        gap: calc(var(--key-size));


        > div {
            display: grid;
            grid-template-columns: repeat(2, var(--key-size));
        }
    }

    &.has-compound-keys .vowel-row > div {
        grid-template-columns: var(--key-size-reduced) var(--key-size-compound) var(--key-size-reduced);
    }
}

</style>