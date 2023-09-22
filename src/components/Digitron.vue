<script>
import "./Digitron.css";
import {
	BROJEVI,
	OPERATIONS,
} from '@/constants/digits.js'

import NumBtn from "./NumBtn.vue";

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
	components: {
		NumBtn
	},
	methods: {
		addToExpression(digit) {
			//Checking to see if the first entered symbol is a number
			if(!parseInt(digit) && this.expression.length === 0 && this.result === '') {
				return;
			} 

			// After we clicked on the = button, the result is shown but the expression array is empty
			// That breaks the app if we want to continue with our calculations
			if(this.result !== '' && parseInt(this.result) && this.expression.length === 0){
				this.expression.push(this.result);
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

			//console.log(`The expression: ${this.expression}`)
		},
		checkForOperators(input) {
			// eslint-disable-next-line
			const regex = new RegExp(/[\+\-\*\/]/);
			
			return regex.test(input);
		},
		evaluateTheExpression(){
			//TODO: Eval of negative integers leads to errors
			//FIX: Wrapp the negative number in parentheses
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
		<!-- <button 
			class="digitron-element_base" 
			v-for="broj in BROJEVI" 
			:key="broj"
			:class="broj === 0 ? 'digitron-button__zero' : 'digitron-button'" 
			@click="addToExpression(broj)"
		>
			{{ broj }}
		</button> -->
		<NumBtn 
			v-for="broj in BROJEVI" 
			:key="broj"
			:addToExpression="addToExpression"
			:broj="broj"
		/>
		<button 
			class="digitron-element_base diigtron-calc"
			@click="evaluateTheExpression"
		>
			=
		</button>
	</div>
</template>
