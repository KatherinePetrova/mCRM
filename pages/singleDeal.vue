<template>
	<div class="main" v-bind:class="{main_none: show}">
		<img src="/arrow.png" class="left bord" @click="show=!show" v-bind:class="{right: show}">
		<div class="left bord" v-if="show">
			<div class="name">
				<h1 style="font-size: 2.5em">{{deal.name}}</h1>
				<div>Этап: {{step.name}}</div>
				<div>Последнее изменение: {{deal.changed}}</div>
			</div>
			<div class="rows">
				<div class="vklad bord tex" v-bind:class="{selected_vklad: vklad==2}" @click="vklad=2">
					+
				</div>
				<div class="vklad bord tex" v-bind:class="{selected_vklad: vklad==1}" @click="vklad=1">
					Дополнительная информация
				</div>
				<div class="vklad bord tex" v-bind:class="{selected_vklad: vklad==0}" @click="vklad=0">
					Основное
				</div>
			</div>
			<div class="left_main bord">
				<div class="main_info bord">
					<label>Ответственный: Мансур</label>
					<label>Бюджет: {{deal.budget}}тг</label>
					<label>Дата создания: {{deal.created}}</label>
				</div>
				<div class="main_info bord">
					<h2>Клиент: {{customer.name}}</h2>
					<label v-for="item in add_customer">{{item.name}}: {{item.text}}</label>
				</div>
				<div class="main_info bord">
					<h2>Исполнитель: {{executor.name}}</h2>
					<label v-for="item in add_executor">{{item.name}}: {{item.text}}</label>
				</div>
			</div>
		</div>
		<div class="right" v-if="show">
			
		</div>
	</div>
</template>

<script>
import axios from 'axios';
export default {
	data(){
		return {
			vklad: 0,
			executor: {},
			customer: {},
			add_customer: [],
			step: {}
		}
	},
	props:['show', 'deal'],
	watch:{
		deal: async function(){
			
			try{
				var step_query = await axios(`http://crm.aziaimport.kz:3000/api/select/step/${this.deal.step}`, {
					method: 'post',
					withCredentials: true
				});

				var customer_query = await axios(`http://crm.aziaimport.kz:3000/api/select/customer/${this.deal.customer}`, {
					method: 'post',
					withCredentials: true
				});

				var add_customer_query = await axios(`http://crm.aziaimport.kz:3000/api/where/add_customer/0`, {
					method: 'post',
					withCredentials: true,
					data: {customer: this.deal.customer}
				});

				this.step = await step_query.data;
				this.customer = await customer_query.data;
				this.add_customer = await add_customer_query.data;

				this.show = true;

			} catch(e){
				alert(e);
			}
		}
	},
	methods:{
	},
	mounted(){
	}
}

</script>

<style scoped>
	div.main_info {
		min-height: calc(20% + 10px);
		width: 95%;
		display: flex;
		justify-content: space-around;
		flex-direction: column;
		border-bottom-style: solid;
		padding-bottom: 10px;
	}
	div.right {
		height: 100%;
		width: 40%;
	}
	div.left_main {
		padding-top: 10px;
		height: 80%;
		width: 100%;
		padding-left: 5%;
		border-top-style: solid;
		background-color: rgb(242, 242, 242);
		display: flex;
		flex-direction: column;
		border-right-style: solid;
		overflow-y: auto;
	}
	div.left {
		height: 100%;
		width: 60%;
		display: flex;
		border-right-style: solid;
		flex-direction: column;
	}
	div.name {
		height: 15%;
		width: 95%;
		display: flex;
		justify-content: center;
		padding-left: 5%;
		flex-direction: column;
		font-size: 0.75em;
	}
	div.rows {
		width: 95%;
		height: 5%;
		padding-left: 5%;
		display: flex;
		z-index: 1;
		flex-direction: row-reverse;
	}
	.vklad {
		height: 101%;
		min-width: 10%;
		border-bottom-style: solid;
		border-top-right-radius: 10px;
		border-top-left-radius: 10px;
		border-right-style: solid;
		border-top-style: solid;
		border-left-style: solid;
		cursor: pointer;
		background-color: rgba(230, 230, 230, 1);
		margin-left: -1%;
		display: flex;
		justify-content: center;
		align-items: center;
		text-align: center;
		padding-left: 10px;
		padding-right: 10px;
	}
	.vklad:hover {
		border-bottom-style: none;
		background-color: rgba(242, 242, 242, 1);
		z-index: 2;
	}
	.selected_vklad {
		border-bottom-style: none;
		background-color: rgba(242, 242, 242, 1);
		z-index: 1;
	}
	img.left {
		transition: 1s;
		transform: rotateY(180deg);
		position: absolute;
		right: 100%;
		background-color: rgba(255, 255, 255, 0.9);
		height: 3vw;
		width: 3vw;
	}
	img.left:hover {
		cursor: pointer;
	}
	img.right {
		transform: rotateY(0deg);
	}
	.main {
		position: fixed;
		width: 0;
		height: calc(100% - 17px - 5vh);
		background-color: rgba(255, 255, 255, 0.9);
		right: 0;
		z-index: 9;
		display: flex;
		transition: 1s;
		top: 5vh;
	}
	.main_none {
		width: 97vw;
	}
</style>