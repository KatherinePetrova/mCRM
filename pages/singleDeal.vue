<template>
	<transition name="modal-fade">
		<div class="bgWindow">
			<div class="win bord">
				<div class="dealInfo">
					<div class="dealName">Сделка</div>
					<nav v-bind:class="activ" v-on:click.prevent>
						<a href="#">Основное</a>
						<a href="#">Доп. инф-ия</a>
					</nav>
					<div class="basic"></div>
					<div class="exInfo"></div>
					<div class="company"></div>
				</div>
				<div class="dealNotes"></div>
			</div>
		</div>
  	</transition>
</template>

<script>
import axios from 'axios';
export default {
	data(){
		return {

		}
	},
	props:[],
	components: {

	},
	methods:{
		closeWin() {
			this.$emit('infoDeal', false);
		},
	},
	mounted(){
		axios.post(`http://localhost:3000/api/select/process/0`).then((res)=>{
			this.procs = res.data
		});
		document.addEventListener("click",() => this.closeWin());
		document.querySelector('.win').addEventListener("click",(event) => event.stopPropagation());
	}
}

</script>

<style scoped>

	.modal-fade-enter,
	.modal-fade-leave-active {
		opacity: 0;
	}

	.modal-fade-enter-active,
	.modal-fade-leave-active {
		transition: opacity .5s ease
	}
	.bgWindow {
		z-index: 999;
		background-color: rgba(0,0,0,.5);
		position: fixed;
		height: 100%;
		width: 100%;
		display: flex;
		justify-content: center;
		align-items: center;
	}
	.win {
		z-index: 1050;
		background-color: white;
		height: 35em;
		width: 80em;
		display: flex;
		justify-content: space-around;
		align-items: center;
		color: rgb(77, 166, 255);
		padding: 0 8em;
		position: relative;
	}

	.close {
		cursor: pointer;
		position: absolute;
		top: .5em;
		right: 1em;

	}
</style>