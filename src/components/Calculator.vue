<template>
    <div class="container">
        <div class="calculator">

            <div v-if="showAnswer" class="display main-display">{{ currentInput }}</div>
            <div v-else class="display main-display">{{ answer }}</div>
            <div v-if="showAnswer" class="display side-display">{{ answer }}</div>
            <div v-else class="display side-display">{{ currentInput }}</div>

            <div class="row top-buttons">
                <div class="button" @click="mathFunctions('sqrt')">&#8730;</div>
                <div class="button exp-button" @click="mathFunctions('squared')">x<sup>2</sup></div>
                <div class="button exp-button" @click="mathFunctions('inverse')">x<sup>-1</sup></div>
                <div class="button" @click="mathFunctions('ln')">ln()</div>
            </div>
            <div class="row">
                <div @click="clear" class="button clear">clear</div>
                <div @click="inputOperation('/')" class="button operation">/</div>
            </div>
            <div class="row">
                <div @click="inputNumber('7')" class="button number">7</div>
                <div @click="inputNumber('8')" class="button number">8</div>
                <div @click="inputNumber('9')" class="button number">9</div>
                <div @click="inputOperation('*')" class="button operation">*</div>
            </div>
            <div class="row">
                <div @click="inputNumber('4')" class="button number">4</div>
                <div @click="inputNumber('5')" class="button number">5</div>
                <div @click="inputNumber('6')" class="button number">6</div>
                <div @click="inputOperation('-')" class="button operation">-</div>
            </div>
            <div class="row">
                <div @click="inputNumber('1')" class="button number">1</div>
                <div @click="inputNumber('2')" class="button number">2</div>
                <div @click="inputNumber('3')" class="button number">3</div>
                <div @click="inputOperation('+')" class="button operation">+</div>
            </div>
            <div class="row">
                
                <div @click="inputNumber('0')" class="button zero-button">0</div>
                <div @click="inputNumber('.')" class="button number">.</div>
                <div @click="calculate" class="button operation">=</div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                currentInput: '',
                currentNumber: '',

                numbersInput: [],
                operations: [],

                lastInput: '',
                answer: '',
                showAnswer: false,
            }
        },
        methods: {
            inputNumber(num) {
                this.currentInput = `${this.currentInput}${num}`;
                this.currentNumber = `${this.currentNumber}${num}`;
            },
            mathFunctions(func) {
                switch(func) {
                    case 'sqrt':
                        if(this.answer) {
                            this.answer = this.formatNumber(Math.pow(this.answer, .5).toString());
                        } else {
                            this.currentInput = this.formatNumber(Math.pow(Number(this.currentNumber), .5).toString());
                            this.currentNumber = this.formatNumber(Math.pow(Number(this.currentNumber), .5).toString());
                        }
                        break;
                    case 'squared':
                        if(this.answer) {
                            this.answer = this.formatNumber(Math.pow(this.answer, .5).toString());
                        } else {
                            this.currentInput = this.formatNumber(Math.pow(Number(this.currentNumber), .5).toString());
                            this.currentNumber = this.formatNumber(Math.pow(Number(this.currentNumber), .5).toString());
                        }
                        break;
                    case 'inverse':
                        if(this.answer) {
                            this.answer = this.formatNumber((1 / this.answer).toString());
                        } else {
                            this.currentInput = this.formatNumber((1 / Number(this.currentNumber)).toString());
                            this.currentNumber = this.formatNumber((1 / Number(this.currentNumber)).toString());
                        }
                        break;
                    case 'ln':
                        if(this.answer) {
                            this.answer = this.formatNumber(Math.log(this.answer).toString());
                        } else {
                            this.currentInput = this.formatNumber(Math.log(Number(this.currentNumber)).toString());
                            this.currentNumber = this.formatNumber(Math.log(Number(this.currentNumber)).toString());
                        }
                        break; 
                }
            },
            clear() {
                this.currentInput = '';
                this.answer = '';
                this.showAnswer = false;
            },
            back() {
                this.currentInput = this.currentInput.slice(0,-1);
            },
            inputOperation(op) {
                const operations = ['+','-','*','/'];
                if(this.currentNumber === ""){
                    this.currentNumber = this.answer;
                    this.currentInput = this.answer;
                    this.numbersInput.push(this.currentNumber);
                    this.operations.push(op);
                    this.currentInput = `${this.currentInput}${op}`
                    this.currentNumber = '';
                }
                
                if(operations.indexOf(this.currentInput.slice(-1)) > -1){
                    this.currentInput = `${this.currentInput.slice(0, this.currentInput.length-1)}${op}`;
                } else {
                    this.numbersInput.push(this.currentNumber);
                    this.operations.push(op);
                    this.currentInput = `${this.currentInput}${op}`
                    this.currentNumber = '';
                }
            },
            calculate() {
                const operations = ['+','-','*','/'];
                if(operations.indexOf(this.currentInput.slice(-1)) === -1){
                    let numbers = [...this.numbersInput];
                    let operations = [...this.operations];
                    numbers.push(this.currentNumber);
                    operations.forEach((operation, index)=>{
                        if(operation === '*' || operation === '/'){
                            if(operation === '*') {
                                numbers[index + 1] = (numbers[index] * numbers[index + 1]).toString();
                                numbers[index] = '';
                                operations[index] = '';
                            }
                            if(operation === '/') {
                                numbers[index + 1] = (numbers[index] / numbers[index + 1]).toString();
                                numbers[index] = '';
                                operations[index] = '';
                            }
                        }
                    })
                    numbers = numbers.filter(Boolean);
                    operations = operations.filter(Boolean);
                    operations.forEach((operation, index) => {
                        if(operation === '+' || operation === '-'){
                            if(operation === '+') {
                                numbers[index + 1] = (Number(numbers[index]) + Number(numbers[index + 1])).toString();
                                numbers[index] = '';
                                operations[index] = '';
                            }
                            if(operation === '-') {
                                numbers[index + 1] = (numbers[index] - numbers[index + 1]).toString();
                                numbers[index] = '';
                                operations[index] = '';
                            }
                        }
                    })
                    numbers = numbers.filter(Boolean);
                    

                    this.answer = this.formatNumber(numbers[0]);

                    this.operations = [];
                    this.numbersInput = [];
                    this.showAnswer = true;
                    this.currentNumber = '';
                    this.lastInput = this.currentInput;
                    this.currentInput = '';
                }
            },
            formatNumber(num) {
                let number = num.toString();
                number = parseFloat(num).toFixed(8);
                return Number(number);
            }
        }
    }
</script>

<style scoped>
.calculator {
  box-sizing: border-box;
  margin: 0 auto;
  width: 250px;
  height: 467px;
  text-align: center;
  box-shadow: 5px 8px 8px #888888;
  background-color: rgb(80,80,80);
  border-radius: 5px;
  border: 1px solid black;
  padding: 6px;
}
.display {
  align-items: center;
  color: #111;
  height:50px;
  background-color: white;
  font-size: 22px;
  border: 1px solid black;
  line-height: 50px;
}
.side-display {
    text-align: right;
    padding-right:5px;
}
.top-buttons {
    padding-top:6px;
}
.row {
    display: flex;
}
.button {
    height:50px;
    width: 50px;
    flex-basis: 25%;
    background-color: rgb(175,175,175);
    border-radius: 3px;
    margin: 3px;
    font-size: 22px;
    color: black;
    line-height: 50px;
    cursor: pointer;
}
.exp-button {
    line-height: 40px;
}
.number {
    background-color: rgb(20,20,20);
    border: 1px solid black;
    
    color: white;
}
.operation {
    background-color: orange;
    border: 1px solid black;
}
.clear {
    flex-basis: 84%;
    
}
.zero-button {
    flex-basis: 55%;
}
</style>