<template>
	<div class="main back">
		<div class="bod bord">
			<nuxt-link class="circle back bord tex" to="/environment">
				<div class="inner_circle bord">
				</div>
				<div class="inner_circle bord" style="background-color: white; transition: 1.5s">
				</div>
				<div class="inner_circle bord" style="transition: 2s">
				</div>
				<label style="z-index: 999">enter</label>
			</nuxt-link>
		</div>
	</div>
</template>

<script>
export default {

}
</script>

<style scoped>
	div.main {
		display: flex;
		justify-content: center;
		align-items: center;
		min-height: 100vh;
	}

	div.bod {
		display: flex;
		justify-content: center;
		align-items: center;
		min-height: 80vh;
		min-width: 100%;
		border-bottom-style: solid;
		border-top-style: solid;
		background-color: white;
		background-image: url('/main.png');
		background-size: cover;
		background-position: center;
	}

	.circle {
		position: absolute;
		min-height: 60%;
		min-width: 30%;
		border-style: solid;
		border-radius: 50%;
		display: flex;
		justify-content: center;
		align-items: center;
		transition: 1s;
		text-decoration: none;
		font-size: 1rem;
	}

	div.inner_circle {
		position: absolute;
		border-style: solid;
		border-radius: 50%;
		background-color: rgba(77, 166, 255, 0.5);
		height: 0;
		width: 0;
		transition: 1s;
	}

	.circle:hover {
		cursor: pointer;
		color: white;
	}

	.circle:hover div.inner_circle {
		height: 100%;
		width: 100%;
	}
	@media screen and (max-width: 600px) {
  		.circle {
    		min-height: 40%;
			min-width: 90%;
  		}
	}
</style>
