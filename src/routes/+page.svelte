<script lang="ts">
    import type { Api } from "chessground/api"
    import { Chessground } from "chessground/chessground"
    import { onMount } from "svelte"
    import "chessground/assets/chessground.base.css"
    import "chessground/assets/chessground.brown.css"
    import "chessground/assets/chessground.cburnett.css"
    import type { Key } from "chessground/types";

    let div: HTMLDivElement;

    let history: [fen: string, move: Key[]][] = []

    let ground: Api;

    onMount(() => {
        ground = Chessground(div, {
            movable: {
                free: true
            },
            animation: {
                enabled: true,
                duration: 200
            },
            events: {
                move: () => {
                    history = [...history, [ground.getFen(), ground.state.lastMove!]];
                }
            },
            drawable: {
                defaultSnapToValidMove: false
            }
        });
    })

    $: if (ground) {
        if (history.length === 0) {
            ground.set({
                fen: "start",
                lastMove: undefined
            })
        } else {
            ground.set({
                fen: history[history.length - 1][0],
                lastMove: history[history.length - 1][1]
            })
        }
    }

    function reset() {
        history = [];
        if (ground) {
            ground.set({
                lastMove: undefined,
                drawable: {
                    shapes: []
                }
            });
        }
    }

    function undo() {
        history = history.slice(0, history.length - 1);
    }
</script>

<main>
    <div class="container">
        <div class="child" bind:this={div}></div>
    </div>

    <div class="history">
        <button disabled={history.length === 0} on:click={reset}>Reset</button>
        <button disabled={history.length === 0} on:click={undo}>Undo</button>
        <p>current fen: {history[history.length - 1]}</p>
    </div>
</main>

<style>
    :global(body) {
        margin: 0;
    }

    main {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        width: 100vw;
        flex-direction: column;
        justify-content: space-evenly;
    }

    div.container {
        width: 500px;
        height: 500px;
    }

    div.history {
        text-align: center;
    }

    div.child {
        display: table;
        width: 100%;
        height: 100%;
    }
</style>
