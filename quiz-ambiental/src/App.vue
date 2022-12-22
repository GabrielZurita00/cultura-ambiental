<script setup>
import { ref, computed } from 'vue'
import imgUrl from './assets/TRIVIA_3-01.png'

const questions = ref([
  {
	question: '¿Dónde lo encontramos en Bolivia?',
	answer: 3,
	options: [
		'Cochabamba, Chuquisaca, Tarija, Potosí',
		'La Paz, Potosí',
		'Chuquisaca, Potosí, Tarija',
		'Chuquisaca, La Paz, Cochabamba, Potosí, Tarija'
	],
	selected: null
  },
  {
	question: '¿Cuál es su importancia en nuestro ecosistema urbano?',
	answer: 2,
	options: [
		'Es una señal de que nuestros ancestros nos envían un mensaje',
		'Mejora el paisaje urbano',
		'Aporta a la polinización de semillas y con esto la protección del ecosistema urbano'
	],
	selected: null
  },
  {
	question: '¿Por qué están desapareciendo estos hermosos seres?',
	answer: 1,
	options: [
		'Se comen basura',
		'El ruido, la contaminación atmosférica y les es difícil cada vez más conseguir alimento',
		'Los gatos que se comen a los colibríes'
	],
	selected: null
  },
  {
	question: '¿Qué podemos hacer para preservar su existencia?',
	answer: 1,
	options: [
		'Alejar depredadores',
		'Sembrar lavanda, kewiña, pacay, salvias, churquis, budjileia, lantana cámara',
		'Aislar el sonido de nuestras casas'
	],
	selected: null
  }
])
const quizStart = ref(false)
const quizCompleted = ref(false)
const currentQuestion = ref(0)
const score = computed(() => {
	let value = 0
	questions.value.map(q => {
		if (q.selected != null && q.answer == q.selected) {
			console.log('correct');
			value++
		}
	})
  if (value==4){
    localStorage.setItem("completed", 1);
    const body = document.querySelector('body')
    body.style.backgroundImage = `url('${imgUrl}')`;
	body.style.backgroundRepeat = 'no-repeat';
	body.style.height = '100%';
	body.style.backgroundPosition = 'center';
	body.style.backgroundSize = 'cover';
  }
	return value
})

const getCurrentQuestion = computed(() => {
	let question = questions.value[currentQuestion.value]
	question.index = currentQuestion.value
	return question
})

const SetAnswer = (e) => {
	questions.value[currentQuestion.value].selected = e.target.value
	e.target.value = null
}

const NextQuestion = () => {
	if (currentQuestion.value < questions.value.length - 1) {
		currentQuestion.value++
		return
	}
	
	quizCompleted.value = true

}

const StartTrivia = () => {
	quizStart.value = true
}
</script>

<template>
	<main class="app">
		<h1>Cultura ambiental: Colibrí</h1>
		<section v-if="!quizStart && !quizCompleted" class="start-section">
			<button @click="StartTrivia" class="start-butt">
				Empezar Trivia
			</button>
		</section>
		<section class="quiz" v-if="!quizCompleted && quizStart">
			<div class="quiz-info">
				<span class="question">{{ getCurrentQuestion.question }}</span>
				<span class="score">Puntos {{ score }}/{{ questions.length }}</span>
			</div>
			
			<div class="options">
				<label 
					v-for="(option, index) in getCurrentQuestion.options" 
					:for="'option' + index" 
					:class="`option ${
						getCurrentQuestion.selected == index 
							? index == getCurrentQuestion.answer 
								? 'correct' 
								: 'wrong'
							: ''
					} ${
						getCurrentQuestion.selected != null &&
						index != getCurrentQuestion.selected
							? 'disabled'
							: ''
					}`">
					<input 
						type="radio" 
						:id="'option' + index" 
						:name="getCurrentQuestion.index" 
						:value="index" 
						v-model="getCurrentQuestion.selected" 
						:disabled="getCurrentQuestion.selected"
						@change="SetAnswer" 
					/>
					<span>{{ option }}</span>
				</label>
			</div>
			
			<button 
				@click="NextQuestion" 
				:disabled="!getCurrentQuestion.selected">
				{{ 
					getCurrentQuestion.index == questions.length - 1 
						? 'Fin' 
						: getCurrentQuestion.selected == null
							? 'Selecciona una opción'
							: 'Siguiente pregunta'
				}}
			</button>
		</section>

		<section v-if="quizStart && quizCompleted">
			<h2>¡Terminó la trivia!</h2>
			<p>Tu puntaje: {{ score }}/{{ questions.length }}</p>
		</section>
	</main>
</template>

<style>
* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
	font-family: 'Montserrat', sans-serif;
}

body {
	background-image: url('./assets/contam.gif'); 
    background-repeat: repeat;
	color: #f1f1f1;
}

.app {
	display: flex;
	flex-direction: column;
	align-items: center;
	padding: 2rem;
	height: 100vh;
}

h1 {
	font-size: 2rem;
	margin-bottom: 2rem;
	color: #093642;
}

.quiz {
	background-color: #e4ccb2;
	padding: 1rem;
	width: 100%;
	max-width: 640px;
}

.quiz-info {
	display: flex;
	justify-content: space-between;
	margin-bottom: 1rem;
}

.quiz-info .question {
	color: #8F8F8F;
	font-size: 1.25rem;
}

.quiz-info.score {
	color: #f1f1f1;
	font-size: 1.25rem;
}

.options {
	margin-bottom: 1rem;
}

.option {
	padding: 1rem;
	display: block;
	background-color: #15422d;
	margin-bottom: 0.5rem;
	border-radius: 0.5rem;
	cursor: pointer;
}

.option:hover {
	background-color: #3c6e51;
}

.option.correct {
	background-color: #2cce7d;
}

.option.wrong {
	background-color: #ff5a5f;
}

.option:last-of-type {
	margin-bottom: 0;
}

.option.disabled {
	opacity: 0.5;
}

.option input {
	display: none;
}

button {
	appearance: none;
	outline: none;
	border: none;
	cursor: pointer;
	padding: 0.5rem 1rem;
	background-color: #013c28;
	color: #1f1f1f;
	font-weight: 700;
	text-transform: uppercase;
	font-size: 1.2rem;
	border-radius: 0.5rem;
}

.start-section{
	height: 50vh;
	width: 50vw;
	margin: 0;
	position: absolute;
	top: 50%;
	-ms-transform: translateY(-50%);
	transform: translateY(-50%);
}

.start-butt{
	appearance: none;
	outline: none;
	border: none;
	cursor: pointer;
	padding: 0.5rem 1rem;
	background-color: #0a013c;
	color: #f1f1f1;
	font-weight: 700;
	text-transform: uppercase;
	font-size: 1.2rem;
	border-radius: 0.5rem;
	width: 50vw;
	height: 10vh;
}

button:disabled {
	opacity: 0.5;
}

h2 {
	font-size: 2rem;
	margin-bottom: 2rem;
	text-align: center;
	color: #03271b;
}

p {
	color: #1f1f1f;
	font-size: 1.5rem;
	text-align: center;
}
</style>
