<template>
  <v-container>
    <v-card
      class="mx-auto"
      width="400"
    >
      <v-card-title>
        <v-text-field
          :label="label"
          variant="outlined"
          :rules="rules"
          ref="answer"
          v-model="input"
          @keydown="nameKeydown($event)"
          ></v-text-field>
      </v-card-title>
      <v-card-text class="bg-surface-light pt-4">
        <v-container class="bg-surface-variant">
          <v-row>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="clearBtn calcBtn" ref="clearBtn" @click="inputClear()">
                AC
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="opBtn calcBtn" ref="openBtn" @click="inputOperator('(')">
                (
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="opBtn calcBtn" ref="closeBtn" @click="inputOperator(')')">
                )
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="opBtn calcBtn" ref="modBtn" @click="inputOperator('^')">
                ^
              </v-btn>
            </v-col>
          </v-row>
          <v-row>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="sevenBtn" @click="inputNumber('7')">
                7
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="eightBtn" @click="inputNumber('8')">
                8
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="nineBtn" @click="inputNumber('9')">
                9
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="opBtn calcBtn" ref="divBtn" @click="inputOperator('/')">
                /
              </v-btn>
            </v-col>
          </v-row>
          <v-row>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="fourBtn" @click="inputNumber('4')">
                4
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="fiveBtn" @click="inputNumber('5')">
                5
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="sixBtn" @click="inputNumber('6')">
                6
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="opBtn calcBtn" ref="multBtn" @click="inputOperator('*')">
                *
              </v-btn>
            </v-col>
          </v-row>
          <v-row>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="threeBtn" @click="inputNumber('3')">
                3
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="twoBtn" @click="inputNumber('2')">
                2
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="oneBtn" @click="inputNumber('1')">
                1
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="opBtn calcBtn" ref="minBtn" @click="inputOperator('-')">
                -
              </v-btn>
            </v-col>
          </v-row>
          <v-row>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="zeroBtn" @click="inputNumber('0')">
                0
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="dotBtn" @click="inputOperator('.')">
                .
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="equalBtn calcBtn" ref="equalBtn" @click="inputEquals()">
                =
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="opBtn calcBtn" ref="plusBtn" @click="inputOperator('+')">
                +
              </v-btn>
            </v-col>
          </v-row>
        </v-container>
      </v-card-text>
    </v-card>
  </v-container>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const answer = ref(null)

onMounted(() => {
  answer.value.validate();
})

</script>

<script>
  export default {
    props: {
      validRules: Array,
      randomNum: String,
      wordleLetter: Number
    },
    data() {
      return {
        input: "",
        instructions: "Enter something!",
        label: "Calculator",
        furthestRule: 1,
        rules: [
          value => {
            if (value) return true;
            this.label = "Calculator";
            return "Input something!";
          },
          (value) => {
            if (this.furthestRule < 2) {
              this.$emit("initializeVariables")
              this.furthestRule = 2;
              this.updateRuleBoard(value);
            }
            let moddedInput = this.input.replaceAll(/\d\(|\)\d/g, (match) => {return match.charAt(0) + "*" + match.charAt(1)}) // Allow num then parenthesis to be read like 5(6)=30

            let numOpenParenthesis = (moddedInput.match(/\(/g) || []).length;
            let numClosedParenthesis = (moddedInput.match(/\)/g) || []).length;
            let parenthesisDiff = numOpenParenthesis - numClosedParenthesis
            parenthesisDiff = parenthesisDiff > 0 ? parenthesisDiff : 0; // ensures non-negative
            moddedInput += ")".repeat(parenthesisDiff); // auto close parenthesis

            moddedInput = moddedInput.replaceAll("^", "**") // replace ^ with exponent operator

            try {
              this.label = eval(moddedInput).toString()
            } catch {
              return "Expression must be valid!";
            }
            return true;
          },
          value => {
            if (this.furthestRule < 3) {
              this.furthestRule = 3;
              this.updateRuleBoard(value);
            }
            if (["+", "-", "*", "/", "^"].every(x => {return value.includes(x)})) {
              return true;
            }
            return "You must use all of the operators at least once!";
          },
          value => {
            if (this.furthestRule < 4) {
              this.furthestRule = 4;
              this.updateRuleBoard(value);
            }
            if (this.label.includes(".") || this.label == "Calculator") {
              return "The answer must be a whole number!";
            }
            return true;
          },
          value => {
            if (this.furthestRule < 5) {
              this.furthestRule = 5;
              this.updateRuleBoard(value);
            }
            if (parseFloat(this.label) % 3 == 0) {
              return true;
            }
            return "The answer must be a multiple of 3!"
          },
          value => {
            if (this.furthestRule < 6) {
              this.furthestRule = 6;
              this.updateRuleBoard(value);
            }
            if (this.input.includes("0")) {
              return "No zeros! That's basically cheating!"
            }
            return true
          },
          value => {
            if (this.furthestRule < 7) {
              this.furthestRule = 7;
              this.updateRuleBoard(value);
            }
            if (parseFloat(this.label) > -12) {
              return "The answer must be less than or equal to -12!";
            }
            return true;
          },
          value => {
            if (this.furthestRule < 8) {
              this.furthestRule = 8;
              this.updateRuleBoard(value);
            }
            if (this.input.includes(this.randomNum.toString())) {
              return true;
            }
            return "The input must contain the number!"
          },
          value => {
            if (this.furthestRule < 9) {
              this.furthestRule = 9;
              this.updateRuleBoard(value);
            }
            if (this.input.includes("/" + this.wordleLetter)) {
              return true;
            }
            return "Go play Wordle!"
          },
          value => {
            if (this.furthestRule < 10) {
              this.furthestRule = 10;
              this.updateRuleBoard(value);
            }
            const date = new Date();
            console.log(date.getMinutes().toString().padStart(2, '0'))
            if (this.input.includes(date.getMinutes().toString().padStart(2, '0'))) {
              return true;
            }
            return "Well, would you look at the time!"
          }
        ]
      }
    },
    methods: {
      inputNumber(num) {
        this.input = this.input.slice(0, this.getCursorLocation()) + num + this.input.slice(this.getCursorLocation());
      },
      inputOperator(op) {
        this.input = this.input.slice(0, this.getCursorLocation()) + op + this.input.slice(this.getCursorLocation());
      },
      inputClear() {
        this.input = "";
      },
      inputEquals() {

      },
      nameKeydown(e) {
        if (!/[\d()^/*\-+=.]/.test(e.key) && e.key.slice(0, 5) != "Arrow" && e.key != "Enter" && e.key != "Backspace") {
          e.preventDefault();
        }
      },
      getCursorLocation() {
        return this.$refs.answer.selectionStart;
      },
      updateRuleBoard(val) {
        const rulesArr = [];
        for (let i = 0; i < this.furthestRule; i++) {
          rulesArr.push(this.rules[i](val) === true ? true : false);
        }
        this.$emit("updateValidRules", rulesArr);
      }
    },
    watch: {
      input(val) {
        this.updateRuleBoard(val === "Calculator" ? "" : val);
      },
      randomNum() {
        this.$refs.answer.validate(this.rules);
      }
    }
  }
</script>

<style>
.btnCol {
  padding: 5px !important;
  height: 50px !important;
}
.calcBtn {
  width: 100% !important;
  height: 100% !important;
}
</style>

