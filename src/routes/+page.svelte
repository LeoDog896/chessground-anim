<script lang="ts">
    import type { Api } from "chessground/api"
    import { Chessground } from "chessground/chessground"
    import { onMount } from "svelte"
    import "chessground/assets/chessground.base.css"
    import "chessground/assets/chessground.brown.css"
    import "chessground/assets/chessground.cburnett.css"
    import type { Key } from "chessground/types";
    import { Chess, SQUARES } from 'chess.js'

    let div: HTMLDivElement;

    let history: [fen: string, move: Key[]][] = []
    let ground: Api;


    // adapted from lichess-examples
    function toDests(fen = "rnbqkbnr/pppppppp/8/8/8/8/PPPPPPPP/RNBQKBNR"): Map<Key, Key[]> {
        const whiteChess = new Chess(fen + " w KQkq - 0 2");
        const blackChess = new Chess(fen + " b KQkq - 0 2");
        
        const dests = new Map();
        SQUARES.forEach(s => {
            const whiteMoves = whiteChess.moves({square: s, verbose: true});
            const blackMoves = blackChess.moves({square: s, verbose: true});

            if (whiteMoves.length) dests.set(s, whiteMoves.map(m => m.to));
            if (blackMoves.length) dests.set(s, blackMoves.map(m => m.to));
        });
        return dests;
    }

    onMount(() => {
        ground = Chessground(div, {
            movable: {
                free: true,
                dests: toDests()
            },
            animation: {
                enabled: true,
                duration: 200
            },
            events: {
                move: () => {
                    console.assert(ground.state.lastMove !== undefined, "lastMove is undefined");
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
                lastMove: undefined,
                check: undefined,
                movable: {
                    free: true,
                    dests: toDests()
                }
            })
        } else {
            const [fen, lastMove] = history[history.length - 1];
            let check: "white" | "black" | undefined = undefined;
            try {
                const whiteGame = new Chess(fen + " w KQkq - 0 2").inCheck();
                const blackGame = new Chess(fen + " b KQkq - 0 2").inCheck();

                if (whiteGame) {
                    check = "white";
                } else if (blackGame) {
                    check = "black";
                }
            } catch (e) {
                console.error(e);
            }

            ground.set({
                fen,
                lastMove,
                check,
                movable: {
                    free: true,
                    dests: toDests(fen)
                }
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
