<script lang="ts">
    import { Chessground } from "chessground/chessground"
    import { onMount } from "svelte"
    import "chessground/assets/chessground.base.css"
    import "chessground/assets/chessground.brown.css"
    import "chessground/assets/chessground.cburnett.css"

    let div: HTMLDivElement;

    let history: string[] = []

    onMount(() => {
        const ground = Chessground(div, {
            movable: {
                free: true
            },
            draggable: {
                enabled: true,
                distance: 3,
                autoDistance: false,
                showGhost: true
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
        })
    })
</script>

<main>
    <div class="container">
        <div class="child" bind:this={div}></div>
    </div>

    {#if false}
    {#each history as move}
        <div>{move}</div>
    {/each}
    {/if}
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
