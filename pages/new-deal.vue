<template>
	<div class="bgWindow">	
		<div class="window">
			<div class="info">
				<div><span>Название сделки: </span></div>
				<div><input class="" v-model="dealName" placeholder="" required></div>
			</div>
			<div class="info">
				<div><span>Заказчик: </span></div>
				<div><select v-model="selected">
					<option disabled value="">Выберите заказчика</option>
					<option v-for="customer in customers" v-bind:value="customers.value">
						{{ customers.text }}
					</option>
				</select></div>
			</div>
			<div class="info">
				<div><span>Исполнитель: </span></div>
				<div><select v-model="selected">
					<option disabled value="">Выберите исполнителя</option>
					<option v-for="executor in executors" v-bind:value="executors.value">
						{{ executors.text }}
					</option>
				</select></div>
			</div>
			<div class="info">
				<div><span>Бюджет: </span></div>
				<div><input class="" v-model="budget" placeholder="" required></div>
			</div>
		</div>
	</div>
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
	.bgWindow {
		z-index: 999;
		background-color: #1241;
		position: absolute;
		display: flex;
		justify-content: center;
		align-items: center;
		height: 100%;
		width: 100%;
	}
	.window {
		background-color: rgb(77, 166, 255);
		height: 50%;
		width: 50%;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		border-radius: 2em;
	}
	.info {
		display: flex;
		justify-content: space-around;
	}
</style>