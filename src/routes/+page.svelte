<script lang="ts">
    import { Chessground } from "chessground/chessground"
    import { onMount } from "svelte"
    import "chessground/assets/chessground.base.css"
    import "chessground/assets/chessground.brown.css"
    import "chessground/assets/chessground.cburnett.css"

    let div: HTMLDivElement;

    onMount(() => {
        const ground = Chessground(div, {
            movable: {
                free: true
            },
            animation: {
                enabled: true,
                duration: 200
            }
        })
    })
</script>

<main>
    <div class="container">
        <div class="child" bind:this={div}></div>
    </div>
</main>

<style>
    :global(body) {
        margin: 0;
    }

    main {
        /* center */
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        
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
