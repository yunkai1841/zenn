---
title: "【1時間】Vue3 + Vuetify3で電卓を作ってみた"
emoji: "🧮"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["vuetify", "vue", "javascript"]
published: false
---

# 概要

# デザイン・外観

# 計算機能

# ボタンイベントの割り当て


# 実装

:::details 電卓本体
```vue:CalculatorPage.vue
<template>
  <div class="calculator-page">
    <!-- display area -->
    <v-col cols="12" class="display-area">
      <v-row>
        <v-col cols="12">
          <v-text-field
            variant="plain"
            hide-details="auto"
            v-model="history"
            class="display-area__history"
            readonly
          />
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="12">
          <v-text-field
            variant="outlined"
            hide-details="auto"
            v-model="input"
            :error="!validateInput(input)"
            :error-messages="validateInput(input) ? [] : ['Invalid input']"
            class="display-area__input"
            readonly
          />
        </v-col>
      </v-row>
    </v-col>
    <!-- buttons area -->
    <v-col cols="12" class="buttons-area">
      <v-row>
        <v-col cols="3">
          <custom-button class="buttons-area__button" @click="clear">C</custom-button>
        </v-col>
        <v-col cols="3">
          <custom-button class="buttons-area__button" @click="backspace">
            <v-icon icon="mdi-backspace" />
          </custom-button>
        </v-col>
        <v-col cols="3">
          <custom-button class="buttons-area__button" @click="plusMinus">
            <v-icon icon="mdi-plus-minus" />
          </custom-button>
        </v-col>
        <v-col cols="3">
          <custom-button class="buttons-area__button" @click="addOperation('/')">
            <v-icon icon="mdi-slash-forward" />
          </custom-button>
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="3">
          <custom-button class="buttons-area__button" :color="numberColor" @click="addNumber(7)">
            7
          </custom-button>
        </v-col>
        <v-col cols="3">
          <custom-button class="buttons-area__button" :color="numberColor" @click="addNumber(8)">
            8
          </custom-button>
        </v-col>
        <v-col cols="3">
          <custom-button class="buttons-area__button" :color="numberColor" @click="addNumber(9)">
            9
          </custom-button>
        </v-col>
        <v-col cols="3">
          <custom-button class="buttons-area__button" @click="addOperation('*')">
            <v-icon icon="mdi-close" />
          </custom-button>
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="3">
          <custom-button class="buttons-area__button" :color="numberColor" @click="addNumber(4)">
            4
          </custom-button>
        </v-col>
        <v-col cols="3">
          <custom-button class="buttons-area__button" :color="numberColor" @click="addNumber(5)">
            5
          </custom-button>
        </v-col>
        <v-col cols="3">
          <custom-button class="buttons-area__button" :color="numberColor" @click="addNumber(6)">
            6
          </custom-button>
        </v-col>
        <v-col cols="3">
          <custom-button class="buttons-area__button" @click="addOperation('-')">
            <v-icon icon="mdi-minus" />
          </custom-button>
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="3">
          <custom-button class="buttons-area__button" :color="numberColor" @click="addNumber(1)">
            1
          </custom-button>
        </v-col>
        <v-col cols="3">
          <custom-button class="buttons-area__button" :color="numberColor" @click="addNumber(2)">
            2
          </custom-button>
        </v-col>
        <v-col cols="3">
          <custom-button class="buttons-area__button" :color="numberColor" @click="addNumber(3)">
            3
          </custom-button>
        </v-col>
        <v-col cols="3">
          <custom-button class="buttons-area__button" @click="addOperation('+')">
            <v-icon icon="mdi-plus" />
          </custom-button>
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="6">
          <custom-button class="buttons-area__button" :color="numberColor" @click="addNumber(0)" :width="2">
            0
          </custom-button>
        </v-col>
        <v-col cols="3">
          <custom-button class="buttons-area__button" @click="decimal">.</custom-button>
        </v-col>
        <v-col cols="3">
          <custom-button class="buttons-area__button" @click="equals">
            <v-icon icon="mdi-equal" />
          </custom-button>
        </v-col>
      </v-row>
    </v-col>
  </div>
</template>

<script setup>
import CustomButton from "./CustomButton.vue";
import { ref } from "vue";

const numberColor = "amber";

const input = ref("");
const history = ref("");

const addNumber = (number) => {
  input.value += number.toString();
};

const clear = () => {
  input.value = "";
  history.value = "";
};

const backspace = () => {
  input.value = input.value.slice(0, -1);
};

const addOperation = (operation) => {
  if (hasOperation(input.value)) {
    equals();
  }
  input.value += operation;
};

const decimal = () => {
  const lastNumber = input.value.split(/\+|-|\/|\*/).reverse()[0]
  if (lastNumber === "") {
    input.value += "0.";
  } else if (!lastNumber.includes(".")) {
    input.value += ".";
  }
};

const plusMinus = () => {
  if (hasOperation(input.value)) {
    equals();
  }
  input.value = (parseInt(input.value) * -1).toString();
};

function hasOperation(input) {
  const operations = ["+", "-", "*", "/"];
  for (let i = 0; i < input.length; i++) {
    if (operations.includes(input[i])) {
      return true;
    }
  }
  return false;
}

const equals = () => {
  if (!validateInput(input.value)) {
    return;
  }
  history.value = input.value;
  input.value = parser(input.value).toString();
};

function parser(input) {
  let result = 0;
  let currentNumber = "";
  let currentOperation = null;
  let minusFlag = false;
  // 先頭がマイナスの場合は、最初の数字をマイナスにする
  if (input[0] === "-") {
    minusFlag = true;
    input = input.slice(1);
  }

  for (let i = 0; i < input.length; i++) {
    const char = input[i];
    if (isNumber(char) || char === ".") {
      if (minusFlag && currentNumber === "") {
        currentNumber = "-";
        minusFlag = false;
      }
      currentNumber += char;
    } else if(isOperation(char)) {
      if (currentOperation === null) {
        result = parseFloat(currentNumber);
      } else {
        result = operate(currentOperation, result, parseFloat(currentNumber));
      }
      currentOperation = char;
      currentNumber = "";
    }
  }
  // 最後の数字を計算に含める
  // オペレーターがない場合は、最初の数字のみ
  if (currentOperation === null) {
    result = parseFloat(currentNumber);
  } else if (currentNumber !== "") {
    result = operate(currentOperation, result, parseFloat(currentNumber));
  }
  // 小数点以下5桁まで
  // 0を削除
  return result.toFixed(5).replace(/\.?0+$/, "");
}

function isNumber(char) {
  const numbers = /[0-9]/;
  return numbers.test(char);
}

function isOperation(char) {
  const operations = /[+*/-]/;
  return operations.test(char);
}

function operate(operation, a, b) {
  switch (operation) {
    case "+":
      return a + b;
    case "-":
      return a - b;
    case "*":
      return a * b;
    case "/":
      return a / b;
    default:
      return 0;
  }
}

function validateInput(input) {
  // 0-9+*/.-のみ
  const regex = /^[0-9+*/.-]+$/;
  return input === "" || regex.test(input);
}

</script>

<style lang="scss" scoped>
.calculator-page {
  align-items: center;
  height: 100%;
  width: fit-content;
  margin: 0 auto;

  .display-area {
    width: 100%;

    .display-area__history :deep(.v-field__input) {
      font-size: 1rem;
      padding: 0;
    }

    .display-area__input :deep(.v-field__input) {
      font-size: 2rem;
      text-align: right;
    }
  }

  .buttons-area {
    width: 100%;

    .buttons-area__button {
      width: 100%;
      height: 100%;
      font-size: 2rem;
    }
  }
}
</style>
```
:::

:::details ボタンコンポーネント
```vue
<template>
  <v-btn
    flat
    :color="color"
    :width="calcWidth"
    :height="calcHeight"
    class="custom-button"
    @click="$emit('click')"
  >
    <span v-if="text" class="custom-button__text">{{ text }}</span>
    <slot v-else />
  </v-btn>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  text: {
    type: String,
  },
  color: {
    type: String,
    default: 'primary',
  },
  // ボタンのサイズを決める基準となる値
  baseSize: {
    type: Number,
    default: 72,
  },
  // baseSizeの倍数で指定する
  width: {
    type: Number,
    default: 1,
  },
  height: {
    type: Number,
    default: 1,
  },
})

defineEmits(['click']);

const calcWidth = computed(() => {
  return `${props.baseSize * props.width}px`
})

const calcHeight = computed(() => {
  return `${props.baseSize * props.height}px`
})

</script>
```
:::