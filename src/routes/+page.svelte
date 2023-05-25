<script lang="ts">
    import type { Api } from "chessground/api"
    import { Chessground } from "chessground/chessground"
    import { onMount } from "svelte"
    import "chessground/assets/chessground.base.css"
    import "chessground/assets/chessground.brown.css"
    import "chessground/assets/chessground.cburnett.css"

    let div: HTMLDivElement;

    let history: string[] = []

    let ground: Api;

    onMount(() => {
        ground = Chessground(div, {
            movable: {
                free: true
            },
            animation: {
                enabled: true,
                duration: 200
            }
        })

        ground.set({
            events: {
                move: () => {
                    history = [...history, ground.getFen()]
                }
            }
        });
    })

    $: if (ground) {
        if (history.length === 0) {
            ground.set({
                fen: "start"
            })
        } else {
            ground.set({
                fen: history[history.length - 1]
            })
        }
    }
</script>

<main>
    <div class="container">
        <div class="child" bind:this={div}></div>
    </div>

    <div class="history">
        <div>
            <button disabled={history.length === 0} on:click={() => history = []}>Reset</button>
            <button disabled={history.length === 0} on:click={() => history = history.slice(0, history.length - 1)}>Undo</button>
        </div>
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
        flex-direction: column;
        
    }
    div.container {
        width: 500px;
        height: 500px;
    }

    div.child {
        display: table;
        width: 100%;
        height: 100%;
    }
</style>
