<script lang="ts">
    import { mount } from "svelte";
    import Square from "./Square.svelte";

    export let level = 0;
    export let pos = [3];
    let root = level === 0;

    let h = level * 360 / 12;
    let s = pos.reduce((a, b) => (b % 2) * 50 + a / 2, 0);
    let l = pos.reduce((a, b) => +(b > 1) * 50 + a / 2, 0);
    
    function clickSquare(event: MouseEvent) {
        // console.log(event);
        if (event.button === 1) {
            middleClickSquare(event);
            return;
        }
        const target = event.target as HTMLElement;
        // console.log("clicked")
        const nextLevel = level + 1;
        while (target.firstChild) target.firstChild.remove();
        for (let i = 0; i < 4; i++) {
            mount(Square, {
                target,
                props: { level: nextLevel, pos: [...pos, i] }
            });
        }
    }
    function middleClickSquare(event: MouseEvent) {
        if (event.button !== 1) return;
        const target = event.target as HTMLElement;
        document.body.querySelector("button")?.replaceWith(target);
        target.style.width = "100vw";
        target.style.height = "100vh";
        target.dataset.root = "true";
    }
    function rightClickSquare(event: MouseEvent) {
        if (event.button !== 2) return;
        const target = event.target as HTMLElement;
        if (target.dataset.root) {
            while (target.firstChild) target.firstChild.remove();
        } else target.remove();
    }
</script>

<button class="square"
    on:click|stopPropagation={clickSquare}
    on:mouseup|stopPropagation={middleClickSquare}
    on:contextmenu|preventDefault|stopPropagation={rightClickSquare}
    style="{level === 0 ? "width: 100vw; height: 100vh;" : ""} background-color: hsl({h}, {s}%, {l}%)"
    data-pos={pos.at(-1)}
    data-root={level === 0 ? true : undefined}
></button>

<style>
    .square {
        border-radius: 0;
        box-sizing: border-box;
        border: none;
        /* outline: 1px solid gray; */
        display: flex;
        position: absolute;
        flex-wrap: wrap;
        margin: 0;
        padding: 0;
        width: 50%;
        height: 50%;
    }
    .square[data-pos="0"] {
        left: 0;
        top: 0;
    }
    .square[data-pos="1"] {
        right: 0;
        top: 0;
    }
    .square[data-pos="2"] {
        left: 0;
        bottom: 0;
    }
    .square[data-pos="3"] {
        right: 0;
        bottom: 0;
    }
</style>