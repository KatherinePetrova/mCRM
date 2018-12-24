<template>
	<div class="process" v-bind:class="{processBack: new_process}">		
		<singleCustomer :singleCus="singleCus" v-if="cusCard" @closeCus="closeCus"></singleCustomer>
		<singleDeal :show="false" :deal="single" v-if="id!=0" @openCustomer="openCustomer"></singleDeal>

		<div class="new_step" v-if="id == 0">
			<form v-on:submit.prevent="sendProcess()" class="circle bord" @click="createProcess()" v-bind:class="{clicked_circle: new_process, back: new_process}">
				<div v-if="new_process">Введите название воронки</div>
				<div v-else>Создать новую воронку</div>
				<input type="text" v-model="label" v-if="new_process" style="margin: 2vh">
			</form>
		</div>
		<div class="new_step" v-else-if="step.length==0">
			<form class="stepInput bord stepCircle" @submit.prevent="createStep">
				<label>Добавить этап</label>
				<input class="stepName" v-model="stepName" placeholder="Новый этап" required>
				<input type="submit" class="btn" value="Добавить">
				<input type="button" class="btn" v-on:click="sendStep()" value="Завершить">
				<input type="button" class="btn" v-on:click="clearStep()" value="Отменить">
			</form>
			<div class="bord single_process tex" v-for="(item, index) in new_step" :key="index">
				<div style="height: 5%; display: flex; justify-content: center; align-items: center;">{{ item.name }}</div>
				<deal :id="0"></deal>
				<div style="" class="foot">
				</div>
			</div>
		</div>
		<div class="bord single_process" v-else-if="step.length!=0" v-for="(item, index) in step" :key="index">
			<div style="height: 5%; display: flex; justify-content: center; align-items: center;">{{ item.name }}</div>
			<deal :id="item.id" v-if="index==0" :first="true" :show='false' :newD="newD"  @openSingle="openSingle"></deal>
			<deal :id="item.id" v-else :newD="newD"  @openSingle="openSingle"></deal>
			<div style="" class="foot">
			</div>
		</div>
	</div>
</template>

<script>
import axios from 'axios';
import deal from './deal';
import singleDeal from './singleDeal';
import singleCustomer from './singleCustomer';
export default {
	components: {
		deal,
		singleDeal,
		singleCustomer
	},
	props: ['id', 'new_process', 'new_step', 'newD'],
	data(){
		return {
			cusCard: false,
			stepName: '',
			step: [],
			label: '',
			single: {
				name: 'Новая'
			},
			singleCus: 0,
		}
	},
	watch: {
		id: function(){
			console.log(this.id);
			axios(`http://crm.aziaimport.kz:3000/api/where/step/0`, {
				data: {process: this.id},
				method: 'post',
				withCredentials: true
			}).then((res)=>{
				console.log(res.data);
				this.step = res.data;
			});
			setTimeout(this.color, 100);
		}
	},
	methods: {
		closeCus(data){
			this.cusCard = data;
		},
		openSingle(data){
			this.single = data;
		},		
		openCustomer(data){
			this.cusCard = !this.cusCard;
			this.singleCus = data;
		},
		getRandomInt(min, max) {
  			return Math.floor(Math.random() * (max - min)) + min;
		},
		color(){
			var procs = document.querySelectorAll('.single_process');
			for(var i=0; i<procs.length; i++){
				procs[i].style.backgroundColor = `rgba(${this.getRandomInt(150, 200)}, ${this.getRandomInt(150, 200)}, ${this.getRandomInt(150, 200)}, 1)`;
				procs[i].style.color = 'white';
			}
		},
		createProcess(){
			if(!this.new_process){
				this.label = '';
				this.new_process = true;
			}
		},
		async sendProcess(){
			try{
				var data = await axios('http://crm.aziaimport.kz:3000/api/insert/process', {
					data: {name: this.label},
					method: 'post',
					withCredentials: true
				});
				this.$emit('insert', data.data)
			} catch(e){
				alert(e);
			}
		},
		createStep(){
				this.new_step.push({name: this.stepName, process: this.id});
				this.stepName = ''
		},
		async sendStep(){
			try{
				for(var i=0; i<this.new_step.length; i++){
					var insert = await axios('http://crm.aziaimport.kz:3000/api/insert/step', {
						data: this.new_step[i],
						method: 'post',
						withCredentials: true
					});
					await this.step.push(insert.data);
					this.color();
				}
			} catch(e){
				alert(e)
			}

		},
		clearStep(){
			this.new_step = [];
		},
		// newDeal(){
		// 	this.$emit('dealId', true);
		// },
	},
	mounted(){
		axios(`http://crm.aziaimport.kz:3000/api/where/step/0`, {
			data: {process: this.id},
			method: 'post',
			withCredentials: true
		}).then((res)=>{
			this.step = res.data
		});
		setTimeout(this.color, 100);
	}
}
</script>

<style scoped>
	.new_step {
		position: relative;
		height: 100%;
		width: 100%;
		display: flex;
		justify-content: center;
		align-items: center;
	}
	.new_deal {
		height: 50%;
		width: 100%;
		display: flex;
		background-color: white;
		justify-content: center;
		align-items: center;
	}
	.foot {
		height: 15%;
		position: relative; 
		display: flex; 
		justify-content: center; 
		align-items: center;
		text-align: center;
		flex-direction: column;
	}
	.single_process {
		width: 100%;
		display: flex;
		min-width: 300px;
		position: relative;
		height: 100%;
		justify-content: center;
		align-items: center;
		flex-direction: column;
		transition: 1s;
		border-style: solid;
		border-color: white;
		color: rgb(77, 166, 255);
		text-transform: capitalize;
	}

	.process {
		position: absolute;
		display: flex;
		width: 100%;
		height: 100%;
		overflow-x: auto;
		align-items: center;
		transition: 1s;
		background-color: rgb(242, 242, 242);
	}

	.stepInput {
		position: absolute;
		z-index: 2;
	}

	.processBack {
		background-color: rgba(77, 166, 255, 0.8);
	}
	
	.stepName {
		width: 15em;
		height: 2em;
		margin-top: .5em;
	}

	.btn {
		margin-top: .5em;
		display: inline-block;
		width: 11em;
		height: 2em;
		line-height: 2em;
		vertical-align: middle;
		text-align: center;
		text-decoration: none;
		user-select: none;
		color: rgb(0,0,0);
		outline: none;
		border: 1px solid rgba(0,0,0,.4);
		border-top-color: rgba(0,0,0,.3);
		border-radius: 2px;
		background: linear-gradient(rgb(255,255,255), rgb(240,240,240));
		box-shadow:
		0 0 3px rgba(0,0,0,0) inset,
		0 1px 1px 1px rgba(255,255,255,.2),
		0 -1px 1px 1px rgba(0,0,0,0);
		transition: .2s ease-in-out;
	}

		.btn:hover:not(:active) {
			box-shadow:
			0 0 3px rgba(0,0,0,0) inset,
			0 1px 1px 1px rgba(0,255,255,.5),
			0 -1px 1px 1px rgba(0,255,255,.5);
		}

		.btn:active {
			background: linear-gradient(rgb(250,250,250), rgb(235,235,235));
			box-shadow:
			0 0 3px rgba(0,0,0,.5) inset,
			0 1px 1px 1px rgba(255,255,255,.4),
			0 -1px 1px 1px rgba(0,0,0,.1);
		}
	.circle {
		position: absolute;
		height: 50vh;
		width: 50vh;
		display: flex;
		justify-content: center;
		align-items: center;
		border-radius: 50%;
		border-style: solid;
		background-color: white;
		color: rgb(77, 166, 255);
		transition: 1s;
		flex-direction: column;
	}
	.circle:hover {
		height: 65vh;
		width: 65vh;
		background-color: rgb(77, 166, 255);
		color: white;
		cursor: pointer;
	}

	.stepCircle {
		position: absolute;
		height: 50vh;
		width: 50vh;
		display: flex;
		justify-content: center;
		align-items: center;
		border-radius: 50%;
		border-style: solid;
		background-color: rgb(77, 166, 255);
		color: white;
		transition: 1s;
		flex-direction: column;
	}

	.clicked_circle {
		height: 65vh;
		width: 65vh;
		border-style: solid;
		border-radius: 50%;
		border-color: white;
	}

	.clicked_circle:hover {
		height: 65vh;
		width: 65vh;
		color: white;
		cursor: initial;
		background-color: rgb(242, 242, 242);
		color: rgb(77, 166, 255);
	}
</style>