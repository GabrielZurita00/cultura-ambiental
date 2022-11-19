<script setup>
import { ref, computed } from 'vue'
import imgUrl from './assets/canvas.png'

const questions = ref([
  {
	question: '¿Qué tipo de residuos se generan más en la ciudad de Cochabamba?',
	answer: 0,
	options: [
		'Residuos orgánicos',
		'Plásticos',
		'Metales'
	],
	selected: null
  },
  {
	question: '¿Cómo se separan los residuos domésticos en Bolivia?',
	answer: 2,
	options: [
		'Orgánicos e inorgánicos',
		'Papel, plástico, orgánicos, metales y basura',
		'Orgánicos, reciclables, no aprovechables'
	],
	selected: null
  },
  {
	question: '¿Cuál es la diferencia entre reciclaje y reuso?',
	answer: 1,
	options: [
		'Es lo mismo',
		'Reuso: Volver a usar un objeto con el mismo fin o uno diferente\nReciclar: Proceso físico-químico por el que pasa un producto para su posterior uso',
		'Reuso es rechazar algo, reciclar es separar los residuos'
	],
	selected: null
  }
])

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
  if (value==3){
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
</script>

<template>
	<main class="app">
		<h1>Cultura ambiental: Basura</h1>
		
		<section class="quiz" v-if="!quizCompleted">
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

		<section v-else>
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
	color: #fff;
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
	color: #FFF;
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
	color: #000000;
	font-weight: 700;
	text-transform: uppercase;
	font-size: 1.2rem;
	border-radius: 0.5rem;
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
	color: #000000;
	font-size: 1.5rem;
	text-align: center;
}
</style>
