<template>
	<div class="main back">
		<singleDeal v-if="infoDeal" @infoDeal="infoDeal"></singleDeal>
		<div class="header bord">
			<div class="menu tex">
				<div class="dropdown">
					<div class="menuex" style="display: flex; background-color: inherit; color: rgb(77, 166, 255); min-height: 100%">
						<div v-if="id==0">Воронки</div>
						<div v-else>Воронка: {{ name }}</div> 
					</div>
					<div class="menuex bord" v-for="item in procs" @click="id=item.id; name=item.name">{{ item.name }}</div>
					<div class="menuex bord" @click="id=0">Создать новую воронку</div>
				</div>
			</div>
			<div class="menu tex" @click="doSmth()">Тест</div>
			<div class="menu tex"></div>
			<div class="menu tex"></div>
			<nuxt-link to="/" class="menu tex" style="color: red; position: absolute; right: 0"	>Выход</nuxt-link>
		</div>
		<div class="body">
			<process :id="id" :new_process="false" :new_step = "[]" @insert="insert" @deal="newDeal" :newD="newD" @infoDeal="infoDeal"></process>
		</div>
	</div>
</template>

<script>
import axios from 'axios';
import process from './process';
import singleDeal from './singleDeal'
export default {
	data(){
		return {
			id: 0,
			name: '',
			procs: [],
			infoDeal: false,
			deal: {
				clicked: false,
				step: 0
			},
			newD: {}
		}
	},
	components: {
		process,
		singleDeal,
	},
	methods:{
		dealSent(data){
			this.newD = data;
		},
		async insert(data){
			this.id = data.id;
			this.name = data.name;
			this.procs.push(data);
		},
		newDeal(dealData){
			this.deal=dealData;
		},
		infoDeal(infdeal){
			this.infoDeal=infdeal;
		},
		async doSmth(){
			try{
				var post = await axios(`http://crm.aziaimport.kz:3000/users/test`, {
					method: 'post',
					withCredentials: true
				});
				console.log(post);	
			} catch(e){
				alert(e);
			}
		}

	},
	mounted(){
		axios(`http://crm.aziaimport.kz:3000/api/select/process/0`, {
			method: 'post',
			withCredentials: true
		}).then((res)=>{
			this.procs = res.data
		}).catch((res)=>{
			this.$router.push('/');
		});
	}
}
</script>

<style scoped>
	div.main {
		display: flex;
		min-height: 100vh;
		flex-direction: column;
		align-items: center;
	}
	div.header {
		position: relative;
		display: flex;
		align-items: center;
		min-width: 100vw;
		min-height: 5vh;
		max-height: 5vh;
		border-bottom-style: solid;
		background-color: white;
		background-image: url('/main.png');
		background-size: cover;
		background-position: 50% 80%;
		background-repeat: no-repeat;
	}
	.menu {
		display: flex;
		justify-content: center;
		align-items: center;
		height: 5vh;
		width: 20%;
		background-color: rgba(255, 255, 255, 0.8);
		transition: .5s;
		text-decoration: none;
		position: relative;
	}

	.menu:hover {
		cursor: pointer;
		background-color: rgba(255, 255, 255, 1);
	}

	.dropdown {
		position: absolute;
		width: 100%;
		height: 100%;
		display: flex;
		align-items: center;
		flex-direction: column;
	}

	.menuex {
		min-height: 70%;
		width: 100%;
		display: none;
		justify-content: center;
		align-items: center;
		transition: 1s;
		z-index: 99;
		background-color: rgba(77, 166, 255, 0.5);
		color: white;
		text-align: center;
		text-transform: capitalize;
	}

	.menuex:hover {
		background-color: rgba(77, 166, 255, 1);
	}

	.dropdown:hover .menuex {
		display: flex;
		
	}

	.body {
		position: relative;
		display: flex;
		min-height: calc(95vh - 0.5px);
		min-width: 100vw;
		justify-content: center;
		align-items: center;
	}
	
</style>