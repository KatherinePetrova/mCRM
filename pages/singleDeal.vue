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
				<div class="vklad bord tex" @click="showTab">
					+
				</div>
				<form class="vklad bord tex selected_vklad" v-if="newTab.show" v-bind:class="{selected_vklad: vklad==4}" @submit.prevent="createTab">
					<input type="text" @focusout="newTab.show = false; vklad=0" v-model="newTab.data" refs='opo'>
				</form>
				<div class="vklad bord tex" v-for="item in tab" v-bind:class="{selected_vklad: vklad==2}" @click="vklad=2">
					{{item.name}}
				</div>
				<div class="vklad bord tex" v-bind:class="{selected_vklad: vklad==1}" @click="vklad=1">
					Дополнительная информация
				</div>
				<div class="vklad bord tex" v-bind:class="{selected_vklad: vklad==0}" @click="vklad=0">
					Основное
				</div>
			</div>
			<div class="left_main bord" v-if="vklad==0">
				<div class="main_info bord">
					<label>Ответственный: {{responsible[0].text}}</label>
					<label>Бюджет: {{deal.budget}}тг</label>
					<label>Дата создания: {{deal.created}}</label>
				</div>
				<div class="main_info bord">
					<h2>Клиент: {{customer.name}}</h2>
					<label v-for="item in add_customer">{{deleteDown(item.name)}}: {{item.text}}</label>
				</div>
				<div class="main_info bord">
					<h2>Исполнитель: {{executor.name}}</h2>
					<label v-for="item in add_executor">{{deleteDown(item.name)}}: {{item.text}}</label>
				</div>
			</div>
			<div class="left_main bord" v-else-if="vklad==1">
				<div class="main_info bord">
					<label v-if="add_document.length==0">Дополнительная информация не найдена</label>
					<label v-for="item in add_document">{{deleteDown(item.name)}}: {{item.text}}</label>
					<form v-if="add_form.show" @submit.prevent="sendDocument">
						<input type="text" placeholder="Тема" v-model="add_form.data.name" required> :
						<input type="text" placeholder="Комментарий" v-model="add_form.data.text" required>
						<label class="tex add" style="margin-left: 10px" @click="add_form.show=false">Отменить</label>
					</form>
					<label class="tex add" @click="add_form.show=true">Добавить +</label>
				</div>
			</div>
			<div class="left_main bord" v-else>
				Приветик
			</div>
		</div>
		<div class="right" v-if="show">
			<div class="chat">
				
			</div>
			<form class="chat">
				<textarea class="chat" placeholder="Примечание"></textarea>
				<input type="file" class="chat">
			</form>
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
			add_customer: {},
			add_executor: {},
			step: {},
			add_document: {},
			responsible: [{
				text: 'Не указано'
			}],
			tab: [],
			newTab: {
				show: false,
				data: {}
			},
			add_form: {
				show: false,
				data: {}
			}
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

				var executor_query = await axios(`http://crm.aziaimport.kz:3000/api/select/executor/${this.deal.executor}`, {
					method: 'post',
					withCredentials: true
				});

				var add_customer_query = await axios(`http://crm.aziaimport.kz:3000/api/where/add_customer/0`, {
					method: 'post',
					withCredentials: true,
					data: {customer: this.deal.customer}
				});

				var add_user_query = await axios(`http://crm.aziaimport.kz:3000/api/where/add_user/0`, {
					method: 'post',
					withCredentials: true,
					data: {user: this.deal.responsible}
				});

				var add_executor_query = await axios(`http://crm.aziaimport.kz:3000/api/where/add_executor/0`, {
					method: 'post',
					withCredentials: true,
					data: {executor: this.deal.executor}
				});

				var add_document_query = await axios(`http://crm.aziaimport.kz:3000/api/where/add_document/0`, {
					method: 'post',
					withCredentials: true,
					data: {deal: this.deal.id}
				});

				this.step = await step_query.data;
				this.customer = await customer_query.data;
				this.add_customer = await add_customer_query.data;
				this.responsible = await add_user_query.data;
				this.executor = await executor_query.data;
				this.add_executor = await add_executor_query.data;
				this.add_document = await add_document_query.data;

				if(this.responsible.length==0){
					this.responsible = [{text: 'Не указан'}]
				}

				this.vklad = 0;
				this.show = true;

			} catch(e){
				alert(e);
			}
		}
	},
	methods:{
		showTab(){
			this.newTab.show=true;
			this.$nextTick(() => this.$refs.opo.focus());
		},
		async sendDocument(){
			try{
				this.add_form.data.deal = this.deal.id;
				var insert = await axios(`http://crm.aziaimport.kz:3000/api/insert/add_document`, {
					method: 'post',
					withCredentials: true,
					data: this.add_form.data
				});

				this.add_document.push(insert.data);
				this.add_form.data = {};
				this.add_form.show = false;

			} catch(e){
				alert(e);
			}
			

		},
		deleteDown(str){
			if(typeof str=='undefined'){
				return ''
			} else {
				var result = str.split('');
				for(var i=0; i<result.length; i++){
					if(result[i]=='_'){
						result[i] = " "
					}
				}
				result  = result.join('');
				return result
			}
			
		}
	},
	mounted(){
	}
}

</script>

<style scoped>
	label.add {
		cursor: pointer;
	}
	textarea.chat {
		height: 70%;
		width: 100%;
		margin-bottom: 5px;
		resize: none;
	}
	form.chat {
		display: flex;
		height: 20%;
		width: 100%;
		flex-direction: column;	
	}
	div.chat {
		height: 80%;
		width: 100%;
	}
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
		background-color: rgb(251, 251, 251);
		padding: 10px;
		display: flex;
		flex-direction: column;
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