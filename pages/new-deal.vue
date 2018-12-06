<template>
	<transition name="modal-fade">
		<div class="bgWindow" @click="$emit('dealId', false)">
		</div>	
			<div class="bgWindow window">
				<div class="info">
					<span>Название сделки: </span>
					<input class="" v-model="dealName" placeholder="" required>
				</div>
				<div class="info">
					<span>Заказчик: </span>
					<select v-model="selected">
						<option disabled value="">Выберите заказчика</option>
						<option v-for="customer in customers" v-bind:value="customers.value">
							{{ customers.text }}
						</option>
					</select>
				</div>
				<div class="info">
					<span>Исполнитель: </span>
					<select v-model="selected">
						<option disabled value="">Выберите исполнителя</option>
						<option v-for="executor in executors" v-bind:value="executors.value">
							{{ executors.text }}
						</option>
					</select>
				</div>
				<div class="info">
					<span>Бюджет: </span>
					<input class="" v-model="budget" placeholder="" required>
				</div>
				<button class="btn" v-on:click="$emit('dealId', false)">Закрыть</button>
			</div>
		<!-- </div> -->
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

	},
	mounted(){
		axios.post(`http://localhost:3000/api/select/process/0`).then((res)=>{
			this.procs = res.data
		});
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
		background-color: #1241;
		position: absolute;
		display: flex;
		justify-content: center;
		align-items: center;
		height: 100%;
		width: 100%;
		flex-direction: column;
	}
	.window {
		z-index: 99999;
		background-color: rgb(77, 166, 255);
		height: 50%;
		width: 50%;
/*		display: flex;
		
		justify-content: center;
		align-items: center;*/
		border-radius: 2em;
	}
	.info {
		display: flex;
		justify-content: space-around;
	}
</style>