<template>
	<transition name="modal-fade">
		<div class="bgWindow">
			<form class="win bord" v-on:submit.prevent="sendDeal">
				<div><h3>Новая сделка</h3><h3 class="close" v-on:click="closeWin()">x</h3></div>
				<div class="info">
					<span>Название сделки: </span>
					<input class="info_input" v-model="form_data.name" placeholder="" required>
				</div>
				<div class="info">
					<span>Заказчик: </span>
					<select v-model="form_data.customer" class="info_input">
						<option disabled value="">Выберите заказчика</option>
						<option v-bind:value="1">
							fdg
						</option>
					</select>
				</div>
				<div class="info">
					<span>Исполнитель: </span>
					<select v-model="form_data.executor" class="info_input">
						<option disabled value="">Выберите исполнителя</option>
						<option v-bind:value="1">
							df
						</option>
					</select>
				</div>
				<div class="info">
					<span>Бюджет: </span>
					<input class="info_input" v-model="form_data.budget" placeholder="" required type="number" min="0">
				</div>
				<input type="submit" class="btn" value="Добавить">
			</form>
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
			form_data: {}
		}
	},
	props:["step"],
	components: {

	},
	methods:{
		closeWin() {
			this.$emit('deal', {clicked:false, step:this.step});
		},
		async sendDeal() {
			this.form_data.step=this.step;
			try {
				var insert = await axios("http://crm.aziaimport.kz:3000/api/insert/deal", {
					method: "post",
					data: this.form_data,
					withCredentials: true
				});
				console.log(insert);
				this.form_data = {};
				this.closeWin();
				this.$emit('dealSent', insert.data);
			} catch(e) {
				console.log(e);
			}
		}
	},
	mounted(){
		axios.post(`http://crm.aziaimport.kz:3000/api/select/process/0`).then((res)=>{
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
		height: 50%;
		width: 50%;
		display: flex;
		justify-content: space-around;
		align-items: center;
		flex-direction: column;
		color: rgb(77, 166, 255);
		padding: 0 8em;
		position: relative;
		border-style: solid;
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