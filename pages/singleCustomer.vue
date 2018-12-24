<template>
	<transition name="modal-fade">
		<div class="bgWindow">
			<div class="win bord">
				<div class="left bord">
					<div class="name">
						<h1 style="font-size: 2.5em">{{customer.name}}</h1>
					</div>
					<div class="rows">
						<div class="vklad bord tex" v-bind:class="{selected_vklad: vklad==1}" @click="vklad=1">
							Сделки
						</div>
						<div class="vklad bord tex" v-bind:class="{selected_vklad: vklad==0}" @click="vklad=0">
							Основное
						</div>
					</div>
					<div class="left_main bord" v-if="vklad==0">
						<div class="main_info bord">
							<h2>Информация: </h2>
							<label v-for="item in add_customer">{{deleteDown(item.name)}}: {{item.text}}</label>
						</div>
					</div>
					<div class="left_main bord" v-else-if="vklad==1">
						<div class="main_info bord">
							<!-- <label v-for="item in deal">{{deleteDown(item.name)}}</label> -->
							<div style="display: flex; flex-direction: column; justify-content: space-around; border-bottom-style: solid; padding: 1em; width: 100%">
								<div style="display: flex; justify-content: space-between;">
									<label>Сделка</label>
									<label>41324 тг.</label>
								</div>
								<div style="border-style: solid; border-width: 1px; padding: .5em;">
									<label style="margin-right: .7em">Воронка</label>
									<label style="background-color: rgb(77, 166, 255); color: white; border-radius: .5em; padding: .3em">Этап</label>	
								</div>
							</div>
						</div>
					</div>
					<div class="left_main bord" v-else>
						Приветик
					</div>
				</div>
				<div class="right">
					<div class="chat">

					</div>
					<form class="chat">
						<textarea class="chat" placeholder="Примечание"></textarea>
						<input type="file" class="chat">
					</form>
				</div>
			</div>
		</div>
	</transition>
</template>

<script>
import axios from 'axios';
export default {
	data(){
		return {
			vklad: 0,
			deal: [],
			add_customer: [],
			customer: {
				name: 'Не указано'
			}
		}
	},
	props:['singleCus'],
	watch:{
		singleCus: async function(){
			console.log(this.singleCus);
			try{
				var customer_query = await axios(`http://crm.aziaimport.kz:3000/api/select/customer/${this.singleCus}`, {
					method: "post",
					withCredentials: true,
				});

				var deal_query = await axios(`http://crm.aziaimport.kz:3000/api/where/deal/0`, {
					method: "post",
					withCredentials: true,
					data: {
						customer: this.singleCus
					}
				});

				var add_customer_query = await axios(`http://crm.aziaimport.kz:3000/api/where/add_customer/0`, {
					method: "post",
					withCredentials: true,
					data: {
						customer: this.singleCus
					}
				});
				this.add_customer = add_customer_query.data;
				this.deal = deal_query.data;
				this.customer = customer_query.data;
			} catch(e){
				alert(e);
			}
		}
	},
	methods:{
		closeWin() {
			this.$emit('closeCus', false);
		},
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
		height: 90%;
		width: 95%;
		display: flex;
		color: rgb(77, 166, 255);
		position: relative;
		border-style: solid;
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
		width: 60%;
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
		width: 40%;
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

</style>