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
          class="calcInput"
          v-model="input"
          @keydown="nameKeydown($event)"
          @click.right.prevent
          ></v-text-field>
      </v-card-title>
      <v-card-text class="bg-surface-light pt-4">
        <v-container class="bg-surface-variant">
          <v-row>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="clearBtn calcBtn" ref="clearBtn" @click="inputClear()" @mousedown.prevent>
                AC
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="opBtn calcBtn" ref="openBtn" @click="inputCharacter('(')" @mousedown.prevent>
                (
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="opBtn calcBtn" ref="closeBtn" @click="inputCharacter(')')" @mousedown.prevent>
                )
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="opBtn calcBtn" ref="modBtn" @click="inputCharacter('^')" @mousedown.prevent>
                ^
              </v-btn>
            </v-col>
          </v-row>
          <v-row>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="sevenBtn" @click="inputCharacter('7')" @mousedown.prevent :disabled="bannedKey==='7'">
                7
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="eightBtn" @click="inputCharacter('8')" @mousedown.prevent :disabled="bannedKey==='8'">
                8
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="nineBtn" @click="inputCharacter('9')" @mousedown.prevent :disabled="bannedKey==='9'">
                9
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="opBtn calcBtn" ref="divBtn" @click="inputCharacter('/')" @mousedown.prevent>
                /
              </v-btn>
            </v-col>
          </v-row>
          <v-row>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="fourBtn" @click="inputCharacter('4')" @mousedown.prevent :disabled="bannedKey==='4'">
                4
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="fiveBtn" @click="inputCharacter('5')" @mousedown.prevent :disabled="bannedKey==='5'">
                5
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="sixBtn" @click="inputCharacter('6')" @mousedown.prevent :disabled="bannedKey==='6'">
                6
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="opBtn calcBtn" ref="multBtn" @click="inputCharacter('*')" @mousedown.prevent>
                *
              </v-btn>
            </v-col>
          </v-row>
          <v-row>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="threeBtn" @click="inputCharacter('1')" @mousedown.prevent :disabled="bannedKey==='1'">
                1
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="twoBtn" @click="inputCharacter('2')" @mousedown.prevent :disabled="bannedKey==='2'">
                2
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="oneBtn" @click="inputCharacter('3')" @mousedown.prevent :disabled="bannedKey==='3'">
                3
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="opBtn calcBtn" ref="minBtn" @click="inputCharacter('-')" @mousedown.prevent>
                -
              </v-btn>
            </v-col>
          </v-row>
          <v-row>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="zeroBtn" @click="inputCharacter('0')" @mousedown.prevent :disabled="furthestRule >= 6">
                0
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="numBtn calcBtn" ref="dotBtn" @click="inputCharacter('.')" @mousedown.prevent>
                .
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" :class="{'apply-shake': shakeEquals, 'equalBtnInactive': !equalsIsActive, 'equalBtnActive': equalsIsActive, 'calcBtn': true}" ref="equalBtn" @click="inputEquals()" @mousedown.prevent>
                =
              </v-btn>
            </v-col>
            <v-col class="btnCol">
              <v-btn variant="outlined" class="opBtn calcBtn" ref="plusBtn" @click="inputCharacter('+')" @mousedown.prevent>
                +
              </v-btn>
            </v-col>
          </v-row>
        </v-container>
      </v-card-text>
    </v-card>
  </v-container>
</template>

<script>
import { CronJob } from 'cron';
import { detect } from 'detect-browser';

  export default {
    mounted() {
      const browser = detect();
      if (browser) {
        this.browserVersion = browser.version.split('.')[0];
      } else {
        this.$toast.error("There was an error detecting which browser this is. What kind of obscure browser are you using?", {duration: 0, dismissible: false, position: "top"})
      }

      this.$refs.answer.validate();

      this.timer = new CronJob('* * * * *', () =>  {
        this.$refs.answer.validate();
        this.updateRuleBoard(this.input);
      })
      this.timer.start();
    },
    beforeUnmount() {
      this.timer.stop();
    },
    props: {
      randomNum: String,
      wordleLetter: Number,
      bannedKey: String
    },
    data() {
      return {
        input: "",
        instructions: "Enter something!",
        label: "Calculator",
        furthestRule: 1,
        confetti: false,
        rules: [
          value => {
            if (value) return true;
            this.label = "Calculator";
            return "Input something!";
          },
          (value) => {
            if (this.furthestRule < 2) {
              this.furthestRule = 2;
              this.updateRuleBoard(value);
            }
            let moddedInput = this.input.replaceAll(/\d\(|\)\d/g, (match) => {return match.charAt(0) + "*" + match.charAt(1)}) // Allow num then parenthesis to be read like 5(6)=30

            let numOpenParenthesis = (moddedInput.match(/\(/g) || []).length;
            let numClosedParenthesis = (moddedInput.match(/\)/g) || []).length;
            let parenthesisDiff = numOpenParenthesis - numClosedParenthesis;
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
              if (this.wordleLetter % 10 === 0) {
                this.$toast.info("Today's Wordle sum ends in a 0, so just add 1 to it so that it doesn't include a 0.", {duration: 0, position: "top"})
              }
            }
            let wordle = this.wordleLetter
            if (this.wordleLetter % 10 === 0) {
              wordle += 1;
            }
            if (this.input.replaceAll(/[()]/g, "").includes("/" + wordle)) {
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
            if (this.input.match(new RegExp("(\\D|^)" + date.getMinutes() + "(\\D|$)")) === null) {
              return true;
            }
            return "Well, would you look at the time!"
          },
          value => {
            if (this.furthestRule < 11) {
              this.furthestRule = 11;
              this.updateRuleBoard(value);
            }
            if (this.input.replaceAll(/[()]/g, "").match(/\+(\d*)-\1(\D|$)|-(\d*)\+\3(\D|$)/) === null) {
              return true;
            }
            return "No cheating by canceling out addition with subtraction!"
          },
          value => {
            if (this.furthestRule < 12) {
              this.furthestRule = 12;
              this.updateRuleBoard(value);
            }
            if (this.input.replaceAll(/[()]/g, "").match(/\*(\d*)\/\1(\D|$)|\/(\d*)\*\3(\D|$)/) === null) {
              return true;
            }
            return "No cheating by canceling out multiplication with division!"
          },
          value => {
            if (this.furthestRule < 13) {
              this.furthestRule = 13;
              this.updateRuleBoard(value);
            }
            if (this.input.replaceAll(/[()]/g, "").match(/(\D|^)(\d*)-\2(\D|$)|-(\d*)\+\4(\D|$)|(\D|^)(\d*)\+-\7(\D|$)|(\D|^)(\d*)-\+\10(\D|$)/) === null) { // this regex could definitely be simplified...
              return true;
            }
            return "No cheating by using subtraction to make 0!"
          },
          value => {
            if (this.furthestRule < 14) {
              this.furthestRule = 14;
              this.updateRuleBoard(value);
            }
            if (this.input.replaceAll(/[()]/g, "").match(/(\D|^)(\d*)\/\2(\D|$)/) === null) {
              return true;
            }
            return "No cheating by making division equal to 1!"
          },
          value => {
            if (this.furthestRule < 15) {
              this.furthestRule = 15;
              this.updateRuleBoard(value);
            }
            if (this.bannedKey === "") {
              return "You have to choose a key to ban, my friend"
            }
            if (this.input.includes(this.bannedKey)) {
              return "Hey, *you* banned " + this.bannedKey + "!"
            }
            return true;
          },
          value => {
            if (this.furthestRule < 16) {
              this.furthestRule = 16;
              this.updateRuleBoard(value);
            }
            if (parseFloat(this.label) === parseFloat(this.browserVersion) * -3) {
              return true;
            }
            return "You're so close!!"
          }
        ],
        timer: null,
        equalsIsActive: false,
        shakeEquals: false,
        browserVersion: 9999999999
      }
    },
    methods: {
      inputCharacter(char) {
        let selectionStart = this.$refs.answer.selectionStart;
        let selectionEnd = this.$refs.answer.selectionEnd;
        this.input = this.input.slice(0, selectionStart) + char + this.input.slice(selectionEnd);
        this.$nextTick(() => {
          this.$refs.answer.selectionStart = selectionStart+1;
          this.$refs.answer.selectionEnd = selectionStart+1;
        })
      },
      inputClear() {
        this.input = "";
      },
      inputEquals() {
        if (this.equalsIsActive) {
          this.$toast.success("You win! Congratulations!!", {duration: 0, position: "top"})
          this.$emit("explodeConfetti");
        } else {
          this.shakeEquals = true;
          setTimeout(() => {
            this.shakeEquals = false;
          }, 1000)
        }
      },
      nameKeydown(e) {
        if (!/[\d()^/*\-+=.]/.test(e.key) && e.key.slice(0, 5) != "Arrow" && e.key != "Enter" && e.key != "Backspace") {
          e.preventDefault();
        }
      },
      getCursorLocation() {
        return this.$refs.answer.selectionEnd;
      },
      updateRuleBoard(val) {
        const rulesArr = [];
        for (let i = 0; i < this.furthestRule; i++) {
          rulesArr.push(this.rules[i](val) === true ? true : false);
        }
        if (rulesArr.every((rule) => rule) && rulesArr.length === this.rules.length) {
          this.equalsIsActive = true;
        } else {
          this.equalsIsActive = false;
        }
        this.$emit("updateValidRules", rulesArr);
      }
    },
    watch: {
      input(val) {
        this.updateRuleBoard(val === "Calculator" ? "" : val);
      },
      randomNum() {
        this.$refs.answer.validate();
        this.updateRuleBoard(this.input);
      },
      bannedKey() {
        this.$refs.answer.validate();
        this.updateRuleBoard(this.input);
      },
      wordleLetter() {
        this.$refs.answer.validate();
        this.updateRuleBoard(this.input);
      }
    }
  }
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Inconsolata:wght@200..900&display=swap');

.btnCol {
  padding: 5px !important;
  height: 50px !important;
}
.calcBtn {
  width: 100% !important;
  height: 100% !important;
}

@keyframes shake {
  10%,
  90% {
    transform: translate3d(-1px, 0, 0);
  }

  20%,
  80% {
    transform: translate3d(2px, 0, 0);
  }

  30%,
  50%,
  70% {
    transform: translate3d(-4px, 0, 0);
  }

  40%,
  60% {
    transform: translate3d(4px, 0, 0);
  }
}

.apply-shake {
  animation: shake 0.82s cubic-bezier(0.36, 0.07, 0.19, 0.97) both;
}

.calcInput {
  font-family: "Inconsolata", monospace;
}
.v-input__details {
  font-family: "Roboto", sans-serif;
}
</style>

