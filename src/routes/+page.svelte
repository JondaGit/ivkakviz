<script lang="ts">
	import { onMount } from 'svelte';

	const letters = [
		'A',
		'B',
		'C',
		'Č',
		'D',
		'E',
		'F',
		'G',
		'H',
		'CH',
		'I',
		'J',
		'K',
		'L',
		'M',
		'N',
		'O',
		'P',
		'R',
		'Ř',
		'S',
		'Š',
		'T',
		'U',
		'V',
		'W',
		'Z',
		'Ž'
	];
	interface Cell {
		state: number;
	}

	// Define the Row interface
	interface Row {
		cells: Cell[];
	}

	// Initialize the array of rows
	let rows: Row[] = [
		{ cells: [{ state: 0 }] }, // 1st row with 1 cell
		{ cells: [{ state: 0 }, { state: 0 }] }, // 2nd row with 2 cells
		{ cells: [{ state: 0 }, { state: 0 }, { state: 0 }] }, // 3rd row with 3 cells
		{ cells: [{ state: 0 }, { state: 0 }, { state: 0 }, { state: 0 }] }, // 4th row with 4 cells
		{ cells: [{ state: 0 }, { state: 0 }, { state: 0 }, { state: 0 }, { state: 0 }] }, // 5th row with 5 cells
		{ cells: [{ state: 0 }, { state: 0 }, { state: 0 }, { state: 0 }, { state: 0 }, { state: 0 }] }, // 6th row with 6 cells
		{
			cells: [
				{ state: 0 },
				{ state: 0 },
				{ state: 0 },
				{ state: 0 },
				{ state: 0 },
				{ state: 0 },
				{ state: 0 }
			]
		} // 7th row with 7 cells
	];
	let my_modal_5: HTMLDialogElement;

	let active_cell: Cell | undefined = undefined;

	function changeActiveCellState(state: number) {
		if (!active_cell) return;
		active_cell.state = state;
		active_cell = undefined;
		rows = rows;
		saveGameState(rows);
	}

	function handleCellClick(cell: Cell) {
		active_cell = cell;
		my_modal_5.showModal();
	}

	function getAbsoluteIndex(rowIndex: number, colIndex: number): number {
		if (rowIndex < 0 || rowIndex >= rows.length) {
			console.error('Invalid row index');
			return 0;
		}

		if (colIndex < 0 || colIndex >= rows[rowIndex].cells.length) {
			console.error('Invalid column index');
			return 0;
		}

		let absoluteIndex = 0;

		for (let i = 0; i < rowIndex; i++) {
			absoluteIndex += rows[i].cells.length;
		}

		absoluteIndex += colIndex;

		return absoluteIndex;
	}

	// Save the game state to localStorage
	function saveGameState(rows: Row[]): void {
		localStorage.setItem('hexagonGameState', JSON.stringify(rows));
	}

	// Load the game state from localStorage
	function loadGameState(): Row[] | null {
		const savedState = localStorage.getItem('hexagonGameState');
		return savedState ? JSON.parse(savedState) : null;
	}

	onMount(() => {
		rows = loadGameState() ?? rows;
	});

	function resetGame() {
		rows = [
			{ cells: [{ state: 0 }] }, // 1st row with 1 cell
			{ cells: [{ state: 0 }, { state: 0 }] }, // 2nd row with 2 cells
			{ cells: [{ state: 0 }, { state: 0 }, { state: 0 }] }, // 3rd row with 3 cells
			{ cells: [{ state: 0 }, { state: 0 }, { state: 0 }, { state: 0 }] }, // 4th row with 4 cells
			{ cells: [{ state: 0 }, { state: 0 }, { state: 0 }, { state: 0 }, { state: 0 }] }, // 5th row with 5 cells
			{
				cells: [{ state: 0 }, { state: 0 }, { state: 0 }, { state: 0 }, { state: 0 }, { state: 0 }]
			}, // 6th row with 6 cells
			{
				cells: [
					{ state: 0 },
					{ state: 0 },
					{ state: 0 },
					{ state: 0 },
					{ state: 0 },
					{ state: 0 },
					{ state: 0 }
				]
			} // 7th row with 7 cells
		];
		saveGameState(rows);
	}
</script>

<div class="w-screen h-screen relative">
	<div class="absolute h-full w-full top-0 left-0">
		<img src="bg.webp" alt="" class="bg-cover h-full w-full opacity-80" />
	</div>
	<img src="majdule.png" alt="" class="absolute left-0 scale-x-[-1] w-14 h-14 opacity-80" />
	<img src="majdule.png" alt="" class="absolute right-0 w-14 h-14 opacity-80" />

	<img
		src="majdule.png"
		alt=""
		class="absolute bottom-20 w-14 h-14 scale-x-[1] rotate-90 opacity-80"
	/>
	<img
		src="majdule.png"
		alt=""
		class="absolute bottom-20 right-0 w-14 h-14 scale-y-[-1] rotate-90 opacity-80"
	/>

	<div class="absolute bottom-5 w-full flex flex-col place-items-center gap-4">
		<audio controls>
			<source src="Brains on Fire.mp3" type="audio/mp3" />
			Your browser does not support the audio element.
		</audio>
		<button class="btn btn-sm z-50" onclick="my_modal_reset.showModal()"
			>🍤 Resetovat todos 🍤</button
		>
	</div>

	<div class="w-full h-full p-2 z-40 relative grid place-items-center pb-20">
		<div
			class="flex flex-col w-min place-items-center mt-18 bg-slate-200 py-2 px-4 pt-20 rounded-md bg-opacity-50 shadow-md scale-95"
		>
			{#each rows as row, rindex}
				<div class="flex flex-row -mt-[82px] sm:-mt-[130px]">
					{#each row.cells as cell, cindex}
						<!-- svelte-ignore a11y-click-events-have-key-events -->
						<!-- svelte-ignore a11y-no-static-element-interactions -->
						<div class="relative h-auto">
							<span
								class="transition-all duration-500 -ml-[3px] -mr-[3px] text-[85px] sm:text-[140px]"
								class:text-white={cell.state == 0}
								class:text-red-500={cell.state == 1}
								class:text-blue-500={cell.state == 2}>&#x2B22;</span
							>
							<div class="absolute w-full h-full top-0 left-0 grid place-items-center">
								<div
									class="w-full h-12 sm:h-24 bg-slate-600 top-0 bottom-0 bg-opacity-0 mt-3 sm:mt-6 sm:text-2xl cursor-pointer grid place-items-center transition-all duration-500 z-[100]"
									on:click={() => handleCellClick(cell)}
									class:opacity-20={cell.state !== 0}
									class:text-white={cell.state !== 0}
								>
									{letters[getAbsoluteIndex(rindex, cindex)]}
								</div>
							</div>
						</div>
					{/each}
				</div>
			{/each}
		</div>
	</div>
</div>

<dialog id="my_modal_5" bind:this={my_modal_5} class="modal modal-bottom">
	<div class="modal-box">
		<h3 class="font-bold text-lg">Těpic feši Majdulelinenko 🫡🤙(čti škrr brr bra bap)🫰</h3>
		<p class="py-4">🍑🤏🥵🥵🥵🙌💥😘</p>
		<div class="modal-action">
			<form method="dialog" class="w-full">
				<div class="w-full flex flex-col gap-2">
					<div class="w-full flex flex-row gap-2">
						<button class="btn btn-error flex-1" on:click={() => changeActiveCellState(1)}
							>Tetová</button
						>
						<button class="btn btn-info flex-1" on:click={() => changeActiveCellState(2)}
							>Nemám vtípek na modrou</button
						>
					</div>
					<div class="w-full flex flex-row gap-2">
						<button class="btn flex-1" on:click={() => changeActiveCellState(0)}>🦷</button>
						<button class="btn btn-neutral flex-1" on:click={() => changeActiveCellState(3)}
							>🌑</button
						>
					</div>
				</div>
			</form>
		</div>
	</div>
	<form method="dialog" class="modal-backdrop">
		<button>close</button>
	</form>
</dialog>

<dialog id="my_modal_reset" class="modal modal-bottom">
	<div class="modal-box">
		<h3 class="font-bold text-lg">Resetovat todos 😱</h3>
		<div class="modal-action">
			<form method="dialog">
				<!-- if there is a button in form, it will close the modal -->
				<button class="btn">Ne prde 🤥</button>
				<button class="btn" on:click={() => resetGame()}>Oka brouku 😈</button>
			</form>
		</div>
	</div>
</dialog>
