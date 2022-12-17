<script>
    import Cell from "./Cell.svelte";

    export let grid;

    function test(e) {
        grid[e.detail.row][e.detail.col] = e.detail.status
    }
</script>

<div class="grid">
    {#each grid as row, i}
        <span class="line">
            {#each row as cell, j}
                <Cell status={cell} row={i} col={j} on:message={test} />
            {/each}
        </span>
    {/each}
</div>

<style>
    .line {
        display: flex;
        flex-grow: 1;
    }
    .grid {
        user-select: none;
        display: flex;
        flex-direction: column;
        gap: 0;
        aspect-ratio: 1;
        margin: auto;
        max-height: 85vh;
        background-color: #848484;
    }
    .line:last-child{
		border-bottom: 1px solid rgb(157, 157, 157);
	}
</style>
