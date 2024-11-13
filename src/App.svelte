<script>
	let initialItems = [
		{ id: 1, name: "Banán", pair: "Banán", image: "banana.png", position: null },
		{ id: 2, name: "Répa", pair: "Répa", image: "carrot.png", position: null },
		{ id: 3, name: "Kocsi", pair: "Kocsi", image: "kocsi.png", position: null },
	];

	let items = [];
	let pairs = [];
	let gameStarted = false;
	let draggedItem = null;

	function shuffle(array) {
		for (let i = array.length - 1; i > 0; i--) {
			const j = Math.floor(Math.random() * (i + 1));
			[array[i], array[j]] = [array[j], array[i]];
		}
		return array;
	}

	function getUniquePairs(items) {
		return [...new Set(items.map((item) => item.pair))];
	}

	function initializeGame() {
		items = shuffle([...initialItems]).map(item => ({
			...item,
			position: getRandomPosition()
		}));
		pairs = shuffle(getUniquePairs(items));
	}

	function beginGame() {
		initializeGame();
		gameStarted = true;
	}

	function handleDragStart(event, item) {
		draggedItem = item;
		event.dataTransfer.setData("text/plain", item.id);
	}

	function handleDrop(event, pair) {
		event.preventDefault();
		const draggedId = event.dataTransfer.getData("text/plain");
		const dragged = items.find((item) => item.id == draggedId);

		if (dragged && dragged.pair === pair) {
			alert(`Helyes! ${dragged.name} párja: ${pair}`);
			const dropRect = event.target.getBoundingClientRect();
			dragged.position = `left: ${dropRect.left}px; top: ${dropRect.top}px;`;
			items = items.filter((item) => item !== dragged);
			pairs = pairs.filter((p) => p !== pair);
		} else {
			alert(`Helytelen! ${dragged ? dragged.name : "Ismeretlen"} nem párosítható ${pair}-val.`);
		}
	}

	function resetGame() {
		gameStarted = false;
		items = [];
		pairs = [];
		draggedItem = null;
		beginGame();
	}

	function getRandomPosition() {
		const minPercent = 10;
		const maxPercent = 80;
		const range = maxPercent - minPercent;
		const x = Math.floor(Math.random() * range) + minPercent;
		const y = Math.floor(Math.random() * range) + minPercent;
		return `left: ${x}%; top: ${y}%;`;
	}
</script>

<div class="container">
	{#if !gameStarted}
		<button class="begin-button" on:click={beginGame}>Játék Indítása</button>
	{/if}

	{#if gameStarted}
		<button class="reset-button" on:click={resetGame}>Újraindítás</button>

		{#each items as item}
			<div
				class="item"
				draggable="true"
				on:dragstart={(event) => handleDragStart(event, item)}
				style={item.position}
			>
				<img src={item.image} alt={item.name} />
			</div>
		{/each}

		{#each pairs as pair}
			<div
				class="pair"
				on:dragover|preventDefault
				on:drop={(event) => handleDrop(event, pair)}
				style={getRandomPosition()}
			>
				{pair}
			</div>
		{/each}
	{/if}
</div>

<style>
	.container {
		position: relative;
		width: 100%;
		height: 100vh;
	}

	.item,
	.pair {
		position: absolute;
		width: 150px;
		height: 150px;
		padding: 10px;
		border-radius: 8px;
		display: flex;
		justify-content: center;
		align-items: center;
		text-align: center;
		cursor: pointer;
	}

	.item {
		background-color: #e0f7fa;
		border: 2px dashed #0288d1;
	}

	.pair {
		background-color: #f1f8e9;
		border: 2px solid #81c784;
	}

	.item img {
		max-width: 100%;
		max-height: 100%;
		object-fit: contain;
	}

	.reset-button,
	.begin-button {
		position: absolute;
		top: 10px;
		left: 50%;
		transform: translateX(-50%);
		padding: 10px 20px;
		font-size: 16px;
		cursor: pointer;
		background-color: #4caf50;
		color: white;
		border: none;
		border-radius: 5px;
	}

	.reset-button {
		top: 60px;
	}
</style>
