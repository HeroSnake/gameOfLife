<script>
	import { createEventDispatcher } from 'svelte'
	export let status
	export let row
	export let col

	const dispatch = createEventDispatcher()
	let mousedown = false

	function click(force) {
		if(!force && !mousedown) {
			return
		}
		if (status == 1) {
			status = 0;
		} else {
			status = 1;
		}
		dispatch('message', {
			row: row,
			col: col,
			status: status
		});
	}

	function init(node) {
		node.addEventListener('mouseover', () => click())
		node.addEventListener('click', () => click(true))
	}
	document.addEventListener('mouseup', () => {
		mousedown = false
	})
	document.addEventListener('mousedown', () => {
		mousedown = true
	})
</script>

<span class="cell" class:alive={status == 1} use:init/>

<style>
	.alive {
		background-color: rgb(124, 0, 181);
	}
	.cell {
		border-top: 1px solid rgb(157, 157, 157);
		flex-grow: 1;
		display: inline-block;
		border-left: 1px solid rgb(157, 157, 157);
	}
	.cell:last-child{
		border-right: 1px solid rgb(157, 157, 157);
	}
	.cell:hover {
		background-color: rgba(255, 255, 255, 0.636);
		cursor: pointer;
	}
</style>
