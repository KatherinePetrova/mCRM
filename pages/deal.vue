<template>
	<div class="drag" v-on:dragover="allowDrop($event)" v-on:drop="drop($event)">
		<div class="deal bord tex" v-for="item in deal" draggable="true" v-on:dragstart="drag($event, item)">
			{{ item.name }}
			{{ item.budget }}тг
		</div>
		<div class="deal bord tex" style="background-color: rgba(77, 166, 255, 0.5); color: white;" @click="downMore()" v-if="(deal.length%10)==0">
			Загрузить далее
		</div>
	</div>
</template>

<script>
import axios from 'axios';
export default {
	props: ['id'],
	data(){
		return {
			deal: [],
			count: 0
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
		}
	},
	methods: {
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
	}

	.deal:hover {
		background-color: rgba(230, 230, 255, 0.8);
		cursor: pointer;
	}
</style>