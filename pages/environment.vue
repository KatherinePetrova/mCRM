<template>
	<div class="main back">
		<!-- <newDeal></newDeal> -->
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
			<div class="menu tex" @click="doSmth()">Сделки</div>
			<div class="menu tex">Календарь</div>
			<div class="menu tex"></div>
			<nuxt-link to="/" class="menu tex" style="color: red; position: absolute; right: 0"	>Выход</nuxt-link>
		</div>
		<div class="body">
			<process :id="id" :new_process="false" :new_step = "[]" @insert="insert"></process>
			
		</div>
	</div>
</template>

<script>
import axios from 'axios';
import process from './process';
import newDeal from './new-deal';
export default {
	data(){
		return {
			id: 0,
			name: '',
			procs: []
		}
	},
	components: {
		process,
		newDeal,
	},
	methods:{
		async insert(data){
			this.id = data.id;
			this.name = data.name;
			this.procs.push(data);
		},
		async doSmth(){
			try{
				var post = await axios(`http://localhost:3000/users/test`, {
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
		axios(`http://localhost:3000/api/select/process/0`, {
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
		justify-content: center;
		min-height: 100vh;
		flex-direction: column;
		align-items: center;
	}
	div.header {
		position: relative;
		display: flex;
		align-items: center;
		min-width: 100vw;
		min-height: 10vh;
		max-height: 10vh;
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
		height: 10vh;
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
		min-height: calc(90vh - 0.5px);
		min-width: 100vw;
		justify-content: center;
		align-items: center;
	}
	
</style>