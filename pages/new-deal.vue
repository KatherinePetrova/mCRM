<template>
	<transition name="modal-fade">
		<div class="bgWindow">
			<div class="win">
				<div><h3>Новая сделка</h3><h3 class="close" v-on:click="closeWin()">x</h3></div>
				<div class="info">
					<span>Название сделки: </span>
					<input class="info_input" v-model="dealName" placeholder="" required>
				</div>
				<div class="info">
					<span>Заказчик: </span>
					<select v-model="selected" class="info_input">
						<option disabled value="">Выберите заказчика</option>
						<option v-for="customer in customers" v-bind:value="customers.value">
							{{ customers.text }}
						</option>
					</select>
				</div>
				<div class="info">
					<span>Исполнитель: </span>
					<select v-model="selected" class="info_input">
						<option disabled value="">Выберите исполнителя</option>
						<option v-for="executor in executors" v-bind:value="executors.value">
							{{ executors.text }}
						</option>
					</select>
				</div>
				<div class="info">
					<span>Бюджет: </span>
					<input class="info_input" v-model="budget" placeholder="" required type="number" min="0">
				</div>
				<button class="btn" v-on:click="">Добавить</button>
			</div>
		</div>
  	</transition>
</template>

<script>
import axios from 'axios';
export default {
	data(){
		return {
			selected: '',
			customers: [],
			executors: [],
		}
	},
	components: {

	},
	methods:{
		closeWin() {
			this.$emit('dealId', false);
		}
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
		background-color: rgba(77, 166, 255, 0.8);
		height: 50%;
		width: 50%;
		border-radius: 2em;
		display: flex;
		justify-content: space-around;
		align-items: center;
		flex-direction: column;
		color: white;
		padding: 0 8em;
		position: relative;
	}
	.info {
		width: 100%;
		display: flex;
		justify-content: space-between;
	}
	.info_input {
		width: 17em;
		height: 2em;
	}
	.close {
		cursor: pointer;
		position: absolute;
		top: .5em;
		right: 1em;
	}
</style>