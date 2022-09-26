<template>
	<div class="correct">
		<div v-if="loader">
			<svg class="whole" width="50" height="50" viewBox="0 0 100 100" fill="none" xmlns="http://www.w3.org/2000/svg">
  <g id="loader">
    <animateTransform
           xlink:href="#loader"
           attributeName="transform"
           attributeType="XML"
           type="rotate"
           from="0 50 50"
           to="360 50 50"
           dur="1s"
           begin="0s"
           repeatCount="indefinite"
           restart="always"
           />
    <path class="a" opacity="0.2" fill-rule="evenodd" clip-rule="evenodd" d="M50 100C77.6142 100 100 77.6142 100 50C100 22.3858 77.6142 0 50 0C22.3858 0 0 22.3858 0 50C0 77.6142 22.3858 100 50 100ZM50 90C72.0914 90 90 72.0914 90 50C90 27.9086 72.0914 10 50 10C27.9086 10 10 27.9086 10 50C10 72.0914 27.9086 90 50 90Z" fill="#66B1DC"/>
    <path class="b" fill-rule="evenodd" clip-rule="evenodd" d="M100 50C100 22.3858 77.6142 0 50 0V10C72.0914 10 90 27.9086 90 50H100Z" fill="green"/>
    </g>
			</svg>
		</div>
		<div class="correct-word" v-if="show">{{correctNumber}}  {{correctWord}}</div>
	</div>
  <div class="game">
		<div class="word word-above" :class="{'active':direction==1}">above</div>
		<div class="word word-before" :class="{'active':direction==3}">before</div>
		<div class="number">{{number}}</div>
		<div class="word word-after" :class="{'active':direction==4}">after</div>
		<div class="word word-below" :class="{'active':direction==2}">below</div>
	</div>
	<button class="play" @click="play">Play</button>
	<div class="copyright">Property of <a href="//lectorweb.com" target="_blank">LectorWeb.com</a></div>

	<div class="settings" @click="showModal = true">
		<svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
  <path stroke-linecap="round" stroke-linejoin="round" d="M10.343 3.94c.09-.542.56-.94 1.11-.94h1.093c.55 0 1.02.398 1.11.94l.149.894c.07.424.384.764.78.93.398.164.855.142 1.205-.108l.737-.527a1.125 1.125 0 011.45.12l.773.774c.39.389.44 1.002.12 1.45l-.527.737c-.25.35-.272.806-.107 1.204.165.397.505.71.93.78l.893.15c.543.09.94.56.94 1.109v1.094c0 .55-.397 1.02-.94 1.11l-.893.149c-.425.07-.765.383-.93.78-.165.398-.143.854.107 1.204l.527.738c.32.447.269 1.06-.12 1.45l-.774.773a1.125 1.125 0 01-1.449.12l-.738-.527c-.35-.25-.806-.272-1.203-.107-.397.165-.71.505-.781.929l-.149.894c-.09.542-.56.94-1.11.94h-1.094c-.55 0-1.019-.398-1.11-.94l-.148-.894c-.071-.424-.384-.764-.781-.93-.398-.164-.854-.142-1.204.108l-.738.527c-.447.32-1.06.269-1.45-.12l-.773-.774a1.125 1.125 0 01-.12-1.45l.527-.737c.25-.35.273-.806.108-1.204-.165-.397-.505-.71-.93-.78l-.894-.15c-.542-.09-.94-.56-.94-1.109v-1.094c0-.55.398-1.02.94-1.11l.894-.149c.424-.07.765-.383.93-.78.165-.398.143-.854-.107-1.204l-.527-.738a1.125 1.125 0 01.12-1.45l.773-.773a1.125 1.125 0 011.45-.12l.737.527c.35.25.807.272 1.204.107.397-.165.71-.505.78-.929l.15-.894z" />
  <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
		</svg>
	</div>

	<transition name="fade" appear>
		<div class="settings-modal" v-if="showModal">
			<button class="close" @click="showModal = false">
				<svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
				<path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
			</svg>
			</button>
			<h2>Settings</h2>

			<div class="timer-counter">
				<h4>Time to reply</h4>
				<div class="counter">
					<button class="minus" @click="minus">
						<svg width="24" height="24" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
						<path stroke-linecap="round" stroke-linejoin="round" d="M19.5 12h-15" />
					</svg>
					</button>
					<input class="count" v-model="count" readonly>
					<button class="plus" @click="plus">
						<svg width="24" height="24" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
						<path stroke-linecap="round" stroke-linejoin="round" d="M12 4.5v15m7.5-7.5h-15" />
					</svg>
					</button>
				</div>
			</div>
		</div>
	</transition>
</template>

<script>

import axios from 'axios';

export default {
  name: 'App',
	data() {
		return {
			show: false,
			loader: false,
			direction: 0,
			number: 0,
			correctNumber: 0,
			correctWord: '',
			numbers: null,
			showModal: false,
			timer: 5000,
			count: 5
		}
	},
	methods: {
		play() {
			this.loader = true;
			this.show = false;
			this.randomDirection(); //получаем направление
			this.randomNumber(); //получаем число
			this.correctWord = this.numbers[this.correctNumber];
			setTimeout(() => {
				this.loader = false;
				this.show = true;
			}, this.timer);
		},
		getNumbers() {
			axios.get('data.json').then(response => {
        this.numbers = response.data;
      });
		},
		randomDirection() {
			this.direction = Math.floor(1 + Math.random() * (4 + 1 - 1));
		},
		randomNumber() {
			this.number = Math.floor(1 + Math.random() * (100 + 1 - 1));
			switch (this.direction) {
				case 1:
					if(this.number <= 10) {
						this.randomNumber();
					}
					else {
						this.correctNumber = this.number-10;
					}
					break;
				case 2:
					if(this.number >= 91) {
						this.randomNumber();
					}
					else {
						this.correctNumber = this.number+10;
					}
					break;
				case 3:
					if(this.number == 1) {
						this.randomNumber();
					}
					else {
						this.correctNumber = this.number-1;
					}
					break;
				case 4:
					if(this.number == 100) {
						this.randomNumber();
					}
					else {
						this.correctNumber = this.number+1;
					}
					break;

				default:
					break;
			}

		},
		minus() {
			if(this.count > 1) {
				this.count -= 1;
				this.timer = this.count * 1000;
			}
		},
		plus() {
			this.count += 1;
			this.timer = this.count * 1000;
		}
	},
	mounted() {
		this.getNumbers(); //получаем весь массив цифр
	}
}
</script>

<style>
* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
}
#app {
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;
	height: 100vh;
	background: #CFDFFF;
	font-family: sans-serif;
}
.correct {
	height: 50px;
	margin-bottom: 40px;
}
.correct-word {
	line-height: 1;
	font-size: 40px;
	background: #69E275;
	box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.25);
	border-radius: 5px;
	padding: 10px;
}
.play {
	background: #5165CD;
	box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.25);
	color:#fff;
	padding:15px 40px;
	border: none;
	border-radius: 5px;
	font-size: 20px;
	margin-top: 40px;
	cursor: pointer;
}
.game {
	width: 100%;
	max-width: 800px;
	padding: 20px;
	margin: 0 auto;
	display: flex;
	flex-wrap: wrap;
	justify-content: center;
	align-items: center;
}
.word {
	width: 100px;
	font-weight: bold;
	font-size: 30px;
}
.number {
	font-size: 60px;
	margin: 30px 40px;
	width: 80px;
	text-align: center;
	background: #FFFFFF;
	box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.25);
	border-radius: 5px;
}
.word-above,
.word-below {
	width: 100%;
	text-align: center;
}
.word-before {
	text-align: right;
}
.word.active {
	color: red;
}
.copyright {
	position: absolute;
	bottom: 5px;
}
.copyright a {
	font-weight: 600;
	color: #000;
}
.settings {
	position: absolute;
	top: 5px;
	right: 5px;
	width: 40px;
	height: 40px;
	cursor: pointer;
}
.settings-modal {
	position: fixed;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	background: #5165CD;
	padding: 20px;
}
.close {
	position: absolute;
	top: 5px;
	right: 5px;
	width: 40px;
	height: 40px;
	cursor: pointer;
	border: none;
	cursor:pointer;
	padding: 0;
	background: none;
	color:#fff;
}
.settings-modal h2 {
	color: #fff;
	text-transform: uppercase;
	text-align: center;
}
.fade-enter-active,
.fade-leave-active {
  transition: transform 0.4s cubic-bezier(0.5, 0, 0.5, 1);
}
.fade-enter-from,
.fade-leave-to {
  transform: translateX(100%);
}
.timer-counter {
	margin-top: 100px;
	display: flex;
	align-items: center;
	justify-content: center;
}
.counter {
	display: flex;
	align-items: center;
	justify-content: center;
	margin-left: 30px;
}
.timer-counter h4 {
	color:#fff;
}
.counter button {
	width: 40px;
	height: 40px;
	border: 2px solid #fff;
	background: none;
	color: #fff;
	outline: none;
	cursor: pointer;
	padding: 0;
}
.counter .minus {
	border-radius: 4px 0 0 4px;
}
.counter .plus {
	border-radius: 0 4px 4px 0;
}
.count {
	background: #fff;
	width: 40px;
	height: 40px;
	font-size: 20px;
	border: none;
	text-align: center;
	outline: none;
}

@media(max-width: 500px){
	.correct {
		font-size: 35px;
	}
	.word {
		font-size: 26px;
	}
	.word-before,
	.word-after {
		width: 75px;
	}
	.number {
		width: 70px;
		margin: 30px;
	}
}
</style>
