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
            <v-col>
              <v-btn variant="outlined" class="clearBtn calcBtn" ref="clearBtn" @click="inputClear()">
                AC
              </v-btn>
            </v-col>
            <v-col>
              <v-btn variant="outlined" class="opBtn calcBtn" ref="openBtn" @click="inputOperator('(')">
                (
              </v-btn>
            </v-col>
            <v-col>
              <v-btn variant="outlined" class="opBtn calcBtn" ref="closeBtn" @click="inputOperator(')')">
                )
              </v-btn>
            </v-col>
            <v-col>
              <v-btn variant="outlined" class="opBtn calcBtn" ref="modBtn" @click="inputOperator('^')">
                ^
              </v-btn>
            </v-col>
          </v-row>
          <v-row>
            <v-col>
              <v-btn variant="outlined" class="numBtn calcBtn" ref="sevenBtn" @click="inputNumber('7')">
                7
              </v-btn>
            </v-col>
            <v-col>
              <v-btn variant="outlined" class="numBtn calcBtn" ref="eightBtn" @click="inputNumber('8')">
                8
              </v-btn>
            </v-col>
            <v-col>
              <v-btn variant="outlined" class="numBtn calcBtn" ref="nineBtn" @click="inputNumber('9')">
                9
              </v-btn>
            </v-col>
            <v-col>
              <v-btn variant="outlined" class="opBtn calcBtn" ref="divBtn" @click="inputOperator('/')">
                /
              </v-btn>
            </v-col>
          </v-row>
          <v-row>
            <v-col>
              <v-btn variant="outlined" class="numBtn calcBtn" ref="fourBtn" @click="inputNumber('4')">
                4
              </v-btn>
            </v-col>
            <v-col>
              <v-btn variant="outlined" class="numBtn calcBtn" ref="fiveBtn" @click="inputNumber('5')">
                5
              </v-btn>
            </v-col>
            <v-col>
              <v-btn variant="outlined" class="numBtn calcBtn" ref="sixBtn" @click="inputNumber('6')">
                6
              </v-btn>
            </v-col>
            <v-col>
              <v-btn variant="outlined" class="opBtn calcBtn" ref="multBtn" @click="inputOperator('*')">
                *
              </v-btn>
            </v-col>
          </v-row>
          <v-row>
            <v-col>
              <v-btn variant="outlined" class="numBtn calcBtn" ref="threeBtn" @click="inputNumber('3')">
                3
              </v-btn>
            </v-col>
            <v-col>
              <v-btn variant="outlined" class="numBtn calcBtn" ref="twoBtn" @click="inputNumber('2')">
                2
              </v-btn>
            </v-col>
            <v-col>
              <v-btn variant="outlined" class="numBtn calcBtn" ref="oneBtn" @click="inputNumber('1')">
                1
              </v-btn>
            </v-col>
            <v-col>
              <v-btn variant="outlined" class="opBtn calcBtn" ref="minBtn" @click="inputOperator('-')">
                -
              </v-btn>
            </v-col>
          </v-row>
          <v-row>
            <v-col>
              <v-btn variant="outlined" class="numBtn calcBtn" ref="zeroBtn" @click="inputNumber('0')">
                0
              </v-btn>
            </v-col>
            <v-col>
              <v-btn variant="outlined" class="numBtn calcBtn" ref="dotBtn" @click="inputOperator('.')">
                .
              </v-btn>
            </v-col>
            <v-col>
              <v-btn variant="outlined" class="equalBtn calcBtn" ref="equalBtn" @click="inputEquals()">
                =
              </v-btn>
            </v-col>
            <v-col>
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
  answer.value.text = "hi";
})

</script>

<script>
  export default {
    props: {
      furthestRule: Number
    },
    data() {
      return {
        input: "",
        instructions: "Enter something!",
        label: "Calculator",
        rules: [
          value => {
            if (value) return true;
            this.label = "Calculator";
            return "Input something!";
          },
          () => {
            if (this.furthestRule < 2) {
              this.$emit("updateFurthestRule", 2)
            }
            let moddedInput = this.input.replaceAll(/\d\(|\)\d/g, (match) => {return match.charAt(0) + "*" + match.charAt(1)}) // Allow num then parenthesis to be read like 5(6)=30

            let numOpenParenthesis = (moddedInput.match(/\(/g) || []).length;
            let numClosedParenthesis = (moddedInput.match(/\)/g) || []).length;
            let parenthesisDiff = numOpenParenthesis - numClosedParenthesis
            parenthesisDiff = parenthesisDiff > 0 ? parenthesisDiff : 0; // ensures non-negative
            moddedInput += ")".repeat(parenthesisDiff); // auto close parenthesis

            moddedInput = moddedInput.replaceAll("^", "**") // replace ^ with exponent operator

            console.log(moddedInput)
            try {
              this.label = eval(moddedInput).toString()
            } catch {
              return "Expression must be valid!";
            }
            return true;
          },
          value => {
            if (this.furthestRule < 3) {
              this.$emit("updateFurthestRule", 3)
            }
            if (["+", "-", "*", "/", "^"].every(x => {return value.includes(x)})) {
              return true;
            }
            return "You must use all of the operators at least once!";
          },
          () => {
            if (this.furthestRule < 4) {
              this.$emit("updateFurthestRule", 4)
            }
            if (this.label.includes(".")) {
              return "The answer must be a whole number!";
            }
            return true;
          },
          () => {
            if (this.furthestRule < 5) {
              this.$emit("updateFurthestRule", 5)
            }
            if (parseInt(this.label) % 3 == 0) {
              return true;
            }
            return "The answer must be a multiple of 3!"
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
        if (!/[\d()^/*\-+=]/.test(e.key) && e.key.slice(0, 5) != "Arrow" && e.key != "Enter" && e.key != "Backspace") {
          e.preventDefault();
        }
      },
      getCursorLocation() {
        return this.$refs.answer.selectionStart;
      }
    }
  }
</script>

