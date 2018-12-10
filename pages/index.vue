<template>
	<div class="main back" v-bind:class="{back_blue: login.clicked, back_blue: register.clicked}">
		<div class="bod bord" v-bind:class="{back_image: !login.clicked, back_blue: login.clicked}">
			<form class="circle back bord tex" style="border-color: white" v-if="login.clicked" v-on:submit.prevent="loginSend()">
				<label style="margin: 0.5em; font-size: 1.25em">Авторизация</label>
				<label style="margin: 0.25em">Имя пользователя</label>
				<input type="text" style="margin: 0.5em; width: 50%" v-model="login.data.name">
				<label style="margin: 0.25em">Пароль</label>
				<input type="password" style="margin: 0.25em; width: 50%" v-model="login.data.password">
				<input type="submit" style="margin: 1em; padding: 0.5em; width: 30%" value="Войти">
				<label class="reg" @click="register.clicked=true; login.clicked=false; login.register={}">Регистрация</label>
			</form>
			<form class="circle back bord tex" style="height: 80vh; border-radius: 0" v-else-if="register.clicked" v-on:submit.prevent="registerSend()">
				<label style="margin: 0.5em; font-size: 1.25em">Регистрация</label>
				<label style="margin: 0.25em">Логин</label>
				<input type="text" style="margin: 0.5em; width: 50%" v-model="register.data.name" required>
				<label style="margin: 0.25em">Пароль</label>
				<input type="password" style="margin: 0.25em; width: 50%" v-model="register.data.password" required>
				<label style="margin: 0.25em">Почтовый ящик</label>
				<input type="email" style="margin: 0.25em; width: 50%" v-model="register.data.email" required>
				<label style="margin: 0.25em;">Добавить дополнительную информацию +</label>
				<input type="submit" style="transition: 1s; margin: 1em; padding: 0.5em; width: 40%" value="Зарегестрироваться">
				<label class="reg" @click="register.clicked=false; login.clicked=true; login.data={}">Авторизация</label>
			</form>
			<div class="circle back bord tex" v-else-if="!login.clicked && !register.clicked" @click="login.clicked=true">
				<div class="inner_circle bord">
				</div>
				<div class="inner_circle bord" style="background-color: white; transition: 1.5s">
				</div>
				<div class="inner_circle bord" style="transition: 2s">
				</div>
				<label style="z-index: 999">enter</label>
			</div>
		</div>
	</div>
</template>

<script>
import axios from 'axios'
export default {
	data(){
		return {
			login: {
				clicked: false,
				data: {}
			},
			register: {
				clicked: false,
				data: {}
			}
		}
	},
	methods: {
		async registerSend(){
			try{
				var insert = await axios('http://crm.aziaimport.kz:3000/users/signup', {
					method: 'post',
					data: this.register.data,
					withCredentials: true
				});
				this.register.data = {};
				this.register.clicked = false;
			} catch(e){
				alert(e);
			}
		},
		async loginSend(){
			try{
				var insert = await axios('http://crm.aziaimport.kz:3000/users/signin', {
					method: 'post',
					data: this.login.data,
					withCredentials: true
				});
				this.$router.push('/environment')
			} catch(e){
				alert(e);
			}
		}
	}

}
</script>

<style scoped>
	label.reg {
		text-decoration: underline;
	}
	label.reg:hover {
		cursor: pointer;
	}
	div.back_image {
		background-color: white;
		background-image: url('/main.png');
		background-size: cover;
		background-position: center;
		
	}
	div.back_blue {
		background-color: rgba(77, 166, 255, 1);
	}
	div.main {
		display: flex;
		justify-content: center;
		align-items: center;
		min-height: 100vh;
		transition: 2s;
	}

	div.bod {
		display: flex;
		justify-content: center;
		align-items: center;
		min-height: 80vh;
		min-width: 100%;
		border-bottom-style: solid;
		border-top-style: solid;
		transition: 2s;
	}

	div.circle {
		position: absolute;
		min-height: 60vh;
		min-width: 60vh;
		border-style: solid;
		border-radius: 50%;
		display: flex;
		justify-content: center;
		align-items: center;
		transition: 1s;
		text-decoration: none;
		font-size: 1rem;
		flex-direction: column;
	}

	form.circle {
		position: absolute;
		min-height: 60vh;
		min-width: 60vh;
		border-style: solid;
		border-radius: 50%;
		display: flex;
		justify-content: center;
		align-items: center;
		transition: 1s;
		text-decoration: none;
		font-size: 1rem;
		flex-direction: column;
	}

	div.inner_circle {
		position: absolute;
		border-style: solid;
		border-radius: 50%;
		background-color: rgba(77, 166, 255, 0.5);
		height: 0;
		width: 0;
		transition: 1s;
	}

	div.circle:hover {
		cursor: pointer;
		color: white;
	}

	div.circle:hover div.inner_circle {
		height: 100%;
		width: 100%;
	}
	@media screen and (max-width: 600px) {
  		.circle {
    		min-height: 40%;
			min-width: 90%;
  		}
	}
</style>
