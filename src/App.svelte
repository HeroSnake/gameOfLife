<script>
    import Grid from "./Grid.svelte"
    import { Icon } from "svelte-awesome"
    import refresh from "svelte-awesome/icons/refresh"
    import pause from "svelte-awesome/icons/pause"
    import play from "svelte-awesome/icons/play"

    let godPlay = false

    let lifespan = 10
    let defaultSize = 80
    let size = defaultSize

    $: grid = new Array(size).fill(null).map(() => new Array(size).fill(0))

    function tick() {
        if (!godPlay) {
            return
        }
        // Create a new grid to store the updated cell states
        let newGrid = grid.map((row) => row.slice());
        // Loop through each cell in the grid
        for (let row = 0; row < grid.length; row++) {
            for (let col = 0; col < grid[row].length; col++) {
                // Count the number of live neighbors for the current cell
                let liveNeighbors = countLiveNeighbors(grid, row, col)
                // console.log(row,col,liveNeighbors);
                // Update the cell's state based on the rules of the Game of Life
                if (grid[row][col] === 1) {
                    // Rule 1: Any live cell with fewer than two live neighbors dies
                    if (liveNeighbors < 2) newGrid[row][col] = 0;
                    // Rule 2: Any live cell with two or three live neighbors lives on
                    else if (liveNeighbors === 2 || liveNeighbors === 3)
                        newGrid[row][col] = 1
                    // Rule 3: Any live cell with more than three live neighbors dies
                    else if (liveNeighbors > 3) newGrid[row][col] = 0
                } else {
                    // Rule 4: Any dead cell with exactly three live neighbors becomes a live cell
                    if (liveNeighbors === 3) newGrid[row][col] = 1
                }
            }
        }
        // Return the updated grid
        grid = newGrid
    }

    function countLiveNeighbors(grid, row, col) {
        // Count the number of live cells in the eight neighboring cells
        let liveNeighbors = 0
        // console.log(`Cell[${row}][${col}] :`);
        for (let r = row - 1; r <= row + 1; r++) {
            for (let c = col - 1; c <= col + 1; c++) {
                // Check that the cell is valid (i.e. not out of bounds)
                if (r >= 0 && r < grid.length && c >= 0 && c < grid[r].length) {
                    // Check that the cell is not the current cell
                    if (!(r === row && c === col)) {
                        // console.log(grid[r][c])
                        // Check if the cell is alive
                        if (grid[r][c] === 1) {
                            liveNeighbors++
                        }
                    }
                }
            }
        }

        // Return the number of live neighbors
        return liveNeighbors
    }
    document.addEventListener("keydown", function (e) {
        switch (e.key) {
            case "r":
                reset()
                break
            case " ":
                godPlay = !godPlay
                break
        }
    });
    function reset() {
        godPlay = false
        size = defaultSize
        grid = new Array(size).fill(null).map(() => new Array(size).fill(0))
    }
    function slide() {
        clearInterval(interval)
        interval = setInterval(tick, 1000 / lifespan)
    }
    let interval = setInterval(tick, 1000 / lifespan) // Update the grid every second
</script>

<div class="menu">
    <img src="../favicon.png" alt="logo" class="logo" />
    <div class="ui">
        <button on:click={() => (godPlay = !godPlay)}>
            {#if godPlay}
                <Icon data={play} />
            {:else}
                <Icon data={pause} />
            {/if}
        </button>
        <button on:click={reset}><Icon data={refresh} /></button>
        <div class="range-container">
            <span>Speed x{lifespan / 10}</span>
            <input
                type="range"
                min="1"
                max="50"
                bind:value={lifespan}
                on:input={slide}
            />
        </div>
        <div class="range-container">
            <span>Size {size}x{size}</span>
            <input type="range" min="10" max="100" step="10" bind:value={size} />
        </div>
    </div>
</div>
<Grid {grid} />

<style>
    .menu {
        display: flex;
        justify-content: center;
        align-items: center;
        gap: 10px;
    }
    .logo {
        width: 40px;
        height: 40px;
    }
    .ui {
        margin: auto;
        display: flex;
        gap: 10px;
        align-items: center;
        padding: 10px;
        overflow-y: scroll;
    }
    button {
        display: flex;
        margin: 0;
    }
    input {
        margin: 0;
    }
    input[type="range"] {
        -webkit-appearance: none; /*nécessaire pour Chrome */
        padding: 0; /* nécessaire pour IE */
        font: inherit; /* même rendu suivant font document */
        outline: none;
        color: #069;
        opacity: 0.3;
        background: #ccc; /* sert pour couleur de fond de la zone de déplacement */
        box-sizing: border-box; /* même modèle de boîte pour tous */
        transition: opacity 0.2s;
        cursor: pointer;
    }
    input[type="range"]::-moz-range-thumb {
        background-color: #ccc
    }
    input[type="range"]::-ms-thumb {
        background: yellow;
        border: none;
    }
    .range-container {
        display: flex;
        align-items: center;
        justify-content: center;
        position: relative;
    }
    .range-container > span {
        position: absolute;
    }
</style>
