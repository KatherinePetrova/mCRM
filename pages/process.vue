<template>
	<div class="process" v-bind:class="{processBack: new_process}">
		<form v-on:submit.prevent="sendProcess()" class="circle bord" v-if="id == 0" @click="createProcess()" v-bind:class="{clicked_circle: new_process, back: new_process}">
			<div v-if="new_process">Введите название воронки</div>
			<div v-else>Создать новую воронку</div>
			<input type="text" v-model="label" v-if="new_process" style="margin: 2vh">
		</form>
		<div class="circle bord" v-else-if="step.length == 0">Создать этапы</div>
		<div class="bord single_process" v-else v-for="(item, index) in step" :key="index">
			<div style="height: 5%; display: flex; justify-content: center; align-items: center;">{{ item.name }}</div>
			<deal :id="item.id"></deal>
			<div style="" class="foot">
				<div class="new_deal tex" v-if="index==0">
					dcdjkcdj
				</div>
				<div style="height: 50%; display: flex; align-items: center; justify-content: center;">Lorem Ipsum Dolor</div>
			</div>
		</div>
	</div>
</template>

<script>
import axios from 'axios';
import deal from './deal';
export default {
	components: {
		deal
	},
	props: ['id', 'new_process'],
	data(){
		return {
			step: [],
			label: ''
		}
	},
	watch: {
		id: function(){
			axios.post(`http://localhost:3000/api/where/step`, {process: this.id}).then((res)=>{
				this.step = res.data;
			});
			setTimeout(this.color, 100);
		}
	},
	methods: {
		getRandomInt(min, max) {
  			return Math.floor(Math.random() * (max - min)) + min;
		},
		color(){
			var procs = document.querySelectorAll('.single_process');
			for(var i=0; i<procs.length; i++){
				procs[i].style.backgroundColor = `rgba(${this.getRandomInt(150, 200)}, ${this.getRandomInt(150, 200)}, ${this.getRandomInt(150, 200)}, 1)`;
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
				var data = await axios.post('http://localhost:3000/api/insert/process', {name: this.label});
				this.$emit('insert', data.data)
			} catch(e){
				alert(e);
			}
		}
	},
	mounted(){
		axios.post(`http://localhost:3000/api/where/step`, {process: this.id}).then((res)=>{
			this.step = res.data
		});
		setTimeout(this.color, 100);
	}
}
</script>

<style scoped>
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
		position: relative;
		height: 100%;
		justify-content: center;
		align-items: center;
		flex-direction: column;
		transition: 1s;
		border-style: solid;
		border-color: white;
		color: white;
	}

	.process {
		position: absolute;
		display: flex;
		width: 100%;
		height: 100%;
		overflow-x: auto;
		justify-content: center;
		align-items: center;
		transition: 1s;
		background-color: rgb(242, 242, 242);
	}

	.processBack {
		background-color: rgba(77, 166, 255, 0.8);
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