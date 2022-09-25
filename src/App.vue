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
			numbers: null
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
			}, 5000);
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
					this.correctNumber = this.number-10;
					break;
				case 2:
					this.correctNumber = this.number+10;
					break;
				case 3:
					this.correctNumber = this.number-1;
					break;
				case 4:
					this.correctNumber = this.number+1;
					break;

				default:
					break;
			}

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
