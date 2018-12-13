<template>
	<div class="drag" v-on:dragover="allowDrop($event)" v-on:drop="drop($event)">
		<div class="deal bord tex" v-if="first" @click="show(true)" v-bind:class="{fast: form.show}">
			<label v-if="form.show==false">Быстрое добавление</label>
			<form class="fast" v-else>
				<input type="text" class="fast" placeholder="Название сделки">
				<input type="text" class="fast" placeholder="Контакт">
				<input type="text" class="fast" placeholder="Исполнитель">
				<input type="text" class="fast" placeholder="Бюзжет">
				<div style="display: flex; justify-content: space-around; width: 100%">
					<input type="submit" class="btn">
					<input type="button" @click="show(false)" value="Отменить" class="btn">
				</div>
			</form>
		</div>
		<div class="deal bord tex" v-for="item in deal" draggable="true" v-on:dragstart="drag($event, item)">
			{{ item.name }},
			{{ item.budget }}тг
		</div>
		<div class="deal bord tex" style="background-color: rgba(77, 166, 255, 0.5); color: white;" @click="downMore()" v-if="(deal.length%10)==0 && deal.length!=0">
			Загрузить далее
		</div>
	</div>
</template>

<script>
import axios from 'axios';
export default {
	props: ['id', 'newD', 'first'],
	data(){
		return {
			deal: [],
			count: 0,
			form: {
				show: false
			}
		}
	},
	watch: {
		id: function(){
			this.deal=[];
			this.count = 0;
			axios(`http://crm.aziaimport.kz:3000/api/where/deal/${this.count}`, {
				data: {step: this.id},
				method: 'post',
				withCredentials: true
			}).then((res)=>{
				this.deal = res.data;
			});
		},
		newD: function(){
			this.deal.unshift(this.newD);
		}
	},
	methods: {
		show(bol){
			this.form.show=bol;
		},
		downMore(){
			this.count = this.count + 10;
			axios(`http://crm.aziaimport.kz:3000/api/where/deal/${this.count}`, {
				data: {step: this.id},
				method: 'post',
				withCredentials: true
			}).then((res)=>{
				for(var i=0; i<res.data.length; i++){
					this.deal.push(res.data[i]);
				}
			});
		},
		drag(event, item){
			event.dataTransfer.setData("text", JSON.stringify(item));
			for(var i=0; i<this.deal.length; i++){
				if(this.deal[i].id==item.id){
					this.deal.splice(i, 1);
				}
			}
		},
		allowDrop(event){
		    event.preventDefault();
		},
		async drop(event){
			event.preventDefault();
			var item = JSON.parse(event.dataTransfer.getData("text"));
			try {
				item.step = this.id;
				await axios(`http://crm.aziaimport.kz:3000/api/update/deal`, {
					data: {id: item.id, step: item.step, changed: true},
					method: 'post',
					withCredentials: true
				});
				this.deal.unshift(item);
			} catch(e){
				alert(e.message);
			}
		}
	},
	mounted(){
		axios(`http://crm.aziaimport.kz:3000/api/where/deal/${this.count}`, {
			data: {step: this.id},
			method: 'post',
			withCredentials: true
		}).then((res)=>{
			this.deal = res.data
		});
	}
}
</script>

<style scoped>
	div.fast {
		border-style: dashed;
		color: rgb(150, 150, 150);
		border-color: rgb(150, 150, 150);
		transition: 1s;
		text-transform: none;
		min-height: 35%;
	}
	form.fast {
		position: relative;
		display: flex;
		flex-direction: column;
		justify-content: space-around;
		align-items: center;
		padding: 0.5em;
		height: 100%;
		width: 100%;
	}
	input.fast {
		width: 100%;
		height: 10%;
		margin-bottom: 0.25em;
		font-size: 1.1em; 
	}
	.btn {

	}
	::-webkit-scrollbar {
	    width: 0px;
	}
	::-webkit-scrollbar-track {
	    background: #f1f1f1; 
	}
	::-webkit-scrollbar-thumb {
	    background: #888; 
	}
	::-webkit-scrollbar-thumb:hover {
	    background: #555; 
	}
	.drag {
		display: flex;
		align-items: center;
		flex-direction: column;
		width: 100%;
		height: 80%;
		background: white;
		overflow-y: auto;
		padding-top: 1px;
	}
	.deal {
		transition: 0.5s;
		display: flex;
		justify-content: center;
		align-items: center;
		flex-direction: column;
		width: 99%;
		min-height: 15%;
		background-color: white;
		border-bottom-style: solid;
		text-align: center;
		text-transform: capitalize;
	}

	.deal:hover {
		background-color: rgba(230, 230, 255, 0.8);
		cursor: pointer;
		color: rgb(255, 128, 0);
	}
</style>