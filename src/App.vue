<template>
  <v-app>
    <v-main>
      <v-toolbar>
        <v-toolbar-title class="text-center">
          The Calculator Game
        </v-toolbar-title>
      </v-toolbar>
      <v-container fluid class="fill-height">
        <v-card
          class="mx-auto"
          max-width="400"
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
    </v-main>
  </v-app>
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
            let moddedInput = this.input.replaceAll(/\d\(|\)\d/g, (match) => {return match.charAt(0) + "*" + match.charAt(1)}) // Allow num then parenthesis to be read like 5(6)=30

            let numOpenParenthesis = (moddedInput.match(/\(/g) || []).length;
            let numClosedParenthesis = (moddedInput.match(/\)/g) || []).length;
            moddedInput += ")".repeat(numOpenParenthesis - numClosedParenthesis); // auto close parenthesis

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
            if (value == "2") {
              return "Input can't have any 2s!";
            }
            return true;
          }
        ]
      }
    },
    methods: {
      inputNumber(num) {
        this.input += num;
        this.label = num;
      },
      inputOperator(op) {
        this.input += op;
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
      }
    }
  }
</script>

<style>
  .equalBtn {
    background-color: #7986CB !important;
  }
  .clearBtn {
    background-color: #F44336 !important;
  }
  .opBtn {
    background-color: #FFA726 !important;
  }
  .numBtn {
    background-color: #E8EAF6 !important;
  }

</style>
