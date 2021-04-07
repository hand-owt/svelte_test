<script>
	let pointTotalNeed = 0;
	let meatPerLoop = 0;

	let error = {
		"pointError" : false,
		"meatError" : false,
		"exclude" : false
	}

	const pointValue = {
		"exPlus": 80800,
		"h90": 261500,
		"h95": 910000,
		"h100": 2680000,
		"h150": 4100000
	}

	let counterResult = {
		"meat" : 0,
		"exPlus": 0,
		"h90": 0,
		"h95": 0,
		"h100": 0,
		"h150": 0
	}

	let exclude = {
		"exPlus": false,
		"h90": false,
		"h95": false,
		"h100": false,
		"h150": false
	}

	function calResult(){
		let checkResult = validateInput();
		if(!checkResult){
			return false;
		}
		resetCounter();
		let pointCounting = parseInt(pointTotalNeed);
		let meatCounting = 0

		hitWhat(pointCounting,meatCounting)

		function hitWhat(point,meatCount){

			let prioritykey = findNear(point);

			if(meatCount >= 20 && point >= 0 && prioritykey == "h150"){
				hitH150();
			}else if(meatCount >= 20 && point >= 0 && prioritykey == "h100" ){
				hitH100();
			}else if(meatCount >= 10 && point >= 0 && prioritykey == "h95"){
				hitH95();
			}else if(meatCount >= 5 && point >= 0 && prioritykey == "h90"){
				hitH90();
			}else if(!exclude.exPlus){
				hitExPlus();
			}else{
				meatCount = 999999999;
			}

			if(point <= 0){
				return false;
			}else{
				try{
					hitWhat(point,meatCount);
				}catch(e){
					setTimeout(function(){
						hitWhat(point,meatCount);
					},10)
				}
			}


			function hitExPlus(){
				point -= pointValue.exPlus;
				meatCount += parseInt(meatPerLoop);
				counterResult.meat += parseInt(meatPerLoop);
				counterResult.exPlus++;
			}
			function hitH150(){
				point -= pointValue.h150;
				meatCount -= 20;
				counterResult.h150++;
			}
			function hitH100(){
				point -= pointValue.h100;
				meatCount -= 20;
				counterResult.h100++;
			}
			function hitH95(){
				point -= pointValue.h95;
				meatCount -= 10;
				counterResult.h95++;
			}
			function hitH90(){
				point -= pointValue.h90;
				meatCount -= 5;
				counterResult.h90++;
			}

			function findNear(point){
				let pointValueArray = Object.entries(pointValue);
				let differentKey;
				let differentVal;

				pointValueArray.forEach(function(array){
					if(exclude[array[0]]){
						return;
					}

					let newDifferent = Math.abs(point - array[1]);
					if(differentVal == undefined || differentVal > newDifferent){
						differentVal = newDifferent;
						differentKey = array[0];
					}
				})

				return differentKey;
			}
		}
	}
	function validateInput(){
		cleanError();
		if(pointTotalNeed < 0 || pointTotalNeed == "" || pointTotalNeed == null || pointTotalNeed > 999999999999){
			error.pointError = true;
			return false;
		}
		if(meatPerLoop <= 0 || meatPerLoop == "" || meatPerLoop == null){
			error.meatError = true;
			return false;
		}

		let excludeArray = Object.entries(exclude);
		let checkFlag = false;
		excludeArray.forEach(function(array){
			if(!array[1]){
				checkFlag = true;
			}
		});
		if(!checkFlag){
			error.exclude = true;
			return false;
		}


		return true;

		function cleanError(){
			error.pointError = false;
			error.meatError = false;
			error.exclude = false;
		}
	}
	function resetCounter(){
		counterResult.meat = 0;
		counterResult.exPlus = 0;
		counterResult.h90 = 0;
		counterResult.h95 = 0;
		counterResult.h100 = 0;
		counterResult.h150 = 0;
	}

</script>

<main>
	<div class="container">
		<div class="selector">
			Point : <input type="text" bind:value={pointTotalNeed} on:keyup={calResult} /> <br>
			Meat : <input type="text" bind:value={meatPerLoop} on:keyup={calResult} /> <br>

			Exclude : <br>
			<label for="selectorEX"><input type="checkbox" id="selectorEX" bind:checked={exclude.exPlus} on:change={calResult} />Ex+</label>
			<label for="h90"><input type="checkbox" id="h90" bind:checked={exclude.h90} on:change={calResult} />90</label>
			<label for="h95"><input type="checkbox" id="h95" bind:checked={exclude.h95} on:change={calResult} />95</label>
			<label for="h100"><input type="checkbox" id="h100" bind:checked={exclude.h100} on:change={calResult} />100</label>
			<label for="h150"><input type="checkbox" id="h150" bind:checked={exclude.h150} on:change={calResult} />150</label>
		</div>
		<div class="counter">
			meat : {counterResult.meat} <br>
			ex+ : {counterResult.exPlus} <br>
			90 : {counterResult.h90} <br>
			95 : {counterResult.h95} <br>
			100 : {counterResult.h100} <br>
			150 : {counterResult.h150} 
		</div>
		<div class="error">
			{#if error.pointError} <div>Point must bigger than 0 or smaller than 999999999999</div> {/if}
			{#if error.meatError} <div>Meat must bigger than 0</div> {/if}
			{#if error.exclude} <div>Must something to include cal</div> {/if}
		</div>
	</div>
</main>

<style>
	.container{
		width:100%;
		max-width:1280px;
		display:flex;
		justify-content: center;
		margin: 0 auto;
		flex-wrap: wrap;
	}
	.selector,.counter{margin:0 20px;}

	.selector .label{display:inline-block;}

	.error{width:100%;text-align: center;color:#F00;}
	.error .errorHide{display:none;}

</style>
