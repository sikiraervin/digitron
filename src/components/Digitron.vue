<script>
import {
	BROJEVI,
	OPERATIONS,
} from '@/constants/digits.js'

export default {
	name: "Digitron",
	data() {
		return {
			result: '',
			expression: [],
			BROJEVI,
			OPERATIONS,
		}
	},
	methods: {
		addToExpression(digit) {
			//Checking to see if the first entered symbol is a number
			if(!parseInt(digit) && this.expression.length === 0) {
				return;
			} 
			
			const lastAddedElement = this.expression.slice(-1)[0];

			if(lastAddedElement === '/' && digit === 0) {
				return;
			}

			// console.log(this.result);

			if(parseInt(digit) || digit === 0){
				//The case when we already did a calculation by pressing the = button 
				if(this.result.length > 0 && this.expression.length === 0) {
					this.result = String(digit);
					this.expression.push(digit);
					return;
				}

				//The case when we already provided a number and an operator
				if (this.checkForOperators(lastAddedElement)) {
					this.result = String(digit);
				} else {
					this.result += String(digit);
				}
				
				//Push it onto the expression array
				this.expression.push(digit);
			} else {
				//We need to check if we already entered an arithmetic operator
				
				//If its the previous element we entered, we simply replace them
				if(this.checkForOperators(lastAddedElement) && this.checkForOperators(digit)) {
					this.expression.pop();
					this.expression.push(digit);
				} else {
					let hasOperatorAlready = false;

					this.expression.map((item) => {
						if(this.checkForOperators(item)) hasOperatorAlready = true;
					});

					if(hasOperatorAlready) {
						//We need to evaluate the expression first
						const rawExpression = this.expression.join('');
						const resultSoFar = eval(rawExpression);
						this.result = String(resultSoFar);

						this.expression = [resultSoFar, digit];
					} else {
						this.expression.push(digit);
					}
				}
				
			}

			// console.log(`The expression: ${this.expression}`)
		},
		checkForOperators(input) {
			// eslint-disable-next-line
			const regex = new RegExp(/[\+\-\*\/]/); 
			const matchFound = regex.test(input);

			return !!matchFound;
		},
		evaluateTheExpression(){
			if(this.expression.length === 0) return;

			const rawExpression = this.expression.join('');
			try{
				this.result = String(eval(rawExpression));
				this.expression = [];
			} catch(error) {
				console.error(error);
			}
		},
		clear() {
			this.result = '';
			this.expression = [];
		}
	}
}
</script>

<template>
	<div class="digitron">
		<input 
			type="text" 
			v-model="result" 
			class="digitron-element_base digitron-result"
			:class="result.length > 18 ? 'digitron-element_medium' : ''"
		/>
		<button 
			class="digitron-element_base digitron-button" 
			v-for="operator in OPERATIONS" 
			:key="operator"
			@click="addToExpression(operator)"
		>
			{{ operator }}
		</button>
		<button 
			class="digitron-element_base digitron-element_c"
			@click="clear"
		>
			C
		</button>
		<button 
			class="digitron-element_base" 
			v-for="broj in BROJEVI" 
			:key="broj"
			:class="broj === 0 ? 'digitron-button__zero' : 'digitron-button'" 
			@click="addToExpression(broj)"
		>
			{{ broj }}
		</button>
		<button 
			class="digitron-element_base diigtron-calc"
			@click="evaluateTheExpression"
		>
			=
		</button>
	</div>
</template>

<style scoped>
.digitron {
	max-width: 50rem;
	display: grid;
	grid-gap: 1rem;
	grid-template-columns: repeat(3, 1fr);
}

.digitron-element_base {
	height: 10rem;
	background: linear-gradient(#250358, #440c66);
	box-shadow: 0 2px 20px rgba(234, 78, 240, 0.2);
	color: white;
	font-size: 5rem;
}

.digitron-element_medium {
	font-size: 3rem;
	width: 50rem;
}

.digitron-button,
.digitron-button__zero {
	width: 100%;
	border: none;
	border-radius: 5px;
	cursor: pointer;
	outline: none;
	transition: background-color 0.2s ease;
	gap: 1rem;
}

.digitron-button__zero {
	grid-column-start: 1;
	grid-column-end: 3;
}

.digitron-element_c {
	grid-column-start: 2;
	grid-column-end: 4;
	cursor: pointer;
}

.digitron-result {
	grid-column-start: 1;
	grid-column-end: 4;
	text-align: right;
}

.diigtron-calc {
	cursor: pointer;
	transition: width 0.2s ease-in-out, 
	height 0.2s ease-in-out;
}

.diigtron-calc:hover{
	width: calc(100% + 10px);
	height: calc(100% + 10px);
}

.digitron-button:hover,
.digitron-button__zero:hover {
	transform: translateY(-5px);
	transition: transform 0.2s;
	box-shadow: 0 2px 30px rgba(234, 78, 240, 0.4);
}
</style>
