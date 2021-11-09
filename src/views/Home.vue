<template>
  <div>
    <div class="calculator">
      <div class="calc-value">{{ showValue }}</div>
      <div class="button-column">
        <div class="button other" @click="clearValue()">AC</div>
        <div class="button other" @click="setMinus()">+/-</div>
        <div class="button other" @click="repalacePercentage()">%</div>
        <div class="button calculation" @click="setCalculation(4)">÷</div>
      </div>
      <div class="button-column">
        <div
          class="button number"
          v-for="(number, index) in numberList[0]"
          :key="index"
          @click="setNumber(number)"
        >
          {{ number }}
        </div>
        <div class="button calculation" @click="setCalculation(3)">×</div>
      </div>
      <div class="button-column">
        <div
          class="button number"
          v-for="(number, index) in numberList[1]"
          :key="index"
          @click="setNumber(number)"
        >
          {{ number }}
        </div>
        <div class="button calculation" @click="setCalculation(2)">-</div>
      </div>
      <div class="button-column">
        <div
          class="button number"
          v-for="(number, index) in numberList[2]"
          :key="index"
          @click="setNumber(number)"
        >
          {{ number }}
        </div>
        <div class="button calculation" @click="setCalculation(1)">+</div>
      </div>
      <div class="button-column">
        <div class="button number double" @click="setNumber(0)">0</div>
        <div class="button number" @click="setDot()">.</div>
        <div class="button calculation" @click="calc()">=</div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
type DataType = {
  value: Number; //計算結果
  showValue: Number; //表示する値
  calculation: Number; //計算方法
  firstValue: Number; //１つ目の数字
  secondValue: Number; //2つ目の数字
  thirdValue: Number; //=連打のための数字
  numberList: number[][];  //ボタンの数字
  flag: boolean; //四則演算を連打する時の計算
  continueFlag: boolean; //数字、演算記号、数字の順に押し、＝を連打する時の計算
  restartFlag: boolean; //2回目以降の計算
};
export default {
  name: "Home",
  data(): DataType {
    return {
      value: 0,
      showValue: 0,
      calculation: 0, // 1 = 足し算, 2 = 引き算, 3 = 掛け算, 4 = 割り算
      firstValue: 0,
      secondValue: 0,
      thirdValue: 0,
      numberList: [
        [7, 8, 9],
        [4, 5, 6],
        [1, 2, 3],
      ],
      flag: true,
      continueFlag: false,
      restartFlag: false,
    };
  },
  methods: {
    setNumber(number: Number) { //６桁までの計算しかしない
      if ((this as any).value == 0) { //初めての計算
        (this as any).value = number;
        (this as any).thirdValue = number;
        (this as any).showValue = (this as any).value;
        (this as any).continueFlag = true;
      } else if ( //数字を入力中
        String((this as any).value).length != 6 &&
        !(this as any).restartFlag
      ) {
        (this as any).thirdValue = String((this as any).value) + String(number);
        (this as any).value = String((this as any).value) + String(number);
        (this as any).showValue = (this as any).value;
        (this as any).continueFlag = true;
      } else if ((this as any).restartFlag) { //計算が終わり、別の計算を開始
        (this as any).value = number;
        (this as any).thirdValue = number;
        (this as any).showValue = number;
        (this as any).continueFlag = true;
        (this as any).restartFlag = false;
      }
    },
    setCalculation(calcNumber: Number) { //どの計算をするか決める
      if (!(this as any).flag) {
        (this as any).continueFlag = false;
        this.calc();
      }
      (this as any).calculation = calcNumber;
      (this as any).firstValue = (this as any).value;
      (this as any).flag = false;
      (this as any).value = 0;
    },
    calc() { //実際に計算する
      (this as any).secondValue = (this as any).value;
      switch ((this as any).calculation) {
        case 1:
          if (!(this as any).continueFlag) {
            (this as any).value =
              Number((this as any).firstValue) +
              Number((this as any).thirdValue);
            if (String((this as any).value).length > 6) { //６桁以上の数字は10のベキ乗で表示
              (this as any).showValue =
                Math.round(
                  ((this as any).value /
                    10 ** (String((this as any).value).length - 1)) *
                    10
                ) /
                  10 +
                "e" +
                (String((this as any).value).length - 1);
            }
            (this as any).firstValue = (this as any).value;
            (this as any).showValue = (this as any).value;
            (this as any).flag = true;
            (this as any).restartFlag = true;
          } else if ((this as any).continueFlag) {
            (this as any).value =
              Number((this as any).firstValue) +
              Number((this as any).secondValue);
            if (String((this as any).value).length > 6) {
              (this as any).showValue =
                Math.round(
                  ((this as any).value /
                    10 ** (String((this as any).value).length - 1)) *
                    10
                ) /
                  10 +
                "e" +
                (String((this as any).value).length - 1);
            }
            (this as any).showValue = (this as any).value;
            (this as any).firstValue = (this as any).value;
            (this as any).flag = true;
            (this as any).continueFlag = false; 
            (this as any).restartFlag = true;
          }
          return;
        case 2:
          if (!(this as any).continueFlag) {
            (this as any).value =
              Number((this as any).firstValue) -
              Number((this as any).thirdValue);
            (this as any).firstValue = (this as any).value;
            (this as any).showValue = (this as any).value;
            (this as any).flag = true;
            (this as any).restartFlag = true;
          } else if ((this as any).continueFlag) {
            (this as any).value =
              Number((this as any).firstValue) -
              Number((this as any).secondValue);
            (this as any).showValue = (this as any).value;
            (this as any).firstValue = (this as any).value;
            (this as any).flag = true;
            (this as any).continueFlag = false; //数字を2回、演算記号を押し、＝連打した時
            (this as any).restartFlag = true;
          }
          return;
        case 3:
          if (!(this as any).continueFlag) {
            (this as any).value =
              Number((this as any).firstValue) *
              Number((this as any).thirdValue);
            (this as any).showValue = (this as any).value;
            if (String((this as any).value).length > 6) {
              (this as any).showValue =
                Math.round(
                  ((this as any).value /
                    10 ** (String((this as any).value).length - 1)) *
                    10
                ) /
                  10 +
                "e" +
                (String((this as any).value).length - 1);
            }
            (this as any).firstValue = (this as any).value;
            (this as any).flag = true;
            (this as any).restartFlag = true;
          } else if ((this as any).continueFlag) {
            (this as any).value =
              Number((this as any).firstValue) *
              Number((this as any).secondValue);
            (this as any).showValue = (this as any).value;
            if (String((this as any).value).length > 6) {
              (this as any).showValue =
                Math.round(
                  ((this as any).value /
                    10 ** (String((this as any).value).length - 1)) *
                    10
                ) /
                  10 +
                "e" +
                (String((this as any).value).length - 1);
            }
            (this as any).firstValue = (this as any).value;
            (this as any).flag = true;
            (this as any).continueFlag = false; //数字を2回、演算記号を押し、＝連打した時
            (this as any).restartFlag = true;
          }
          return;
        case 4:
          if (!(this as any).continueFlag) {
            (this as any).value =
              Number((this as any).firstValue) /
              Number((this as any).thirdValue);
            (this as any).value =
              Math.round((this as any).value * 10000) / 10000;
            (this as any).firstValue = (this as any).value;
            (this as any).showValue = (this as any).value;
            (this as any).flag = true;
            (this as any).restartFlag = true;
          } else if ((this as any).continueFlag) {
            (this as any).value =
              Number((this as any).firstValue) /
              Number((this as any).secondValue);
            (this as any).value =
              Math.round((this as any).value * 10000) / 10000;
            (this as any).firstValue = (this as any).value;
            (this as any).showValue = (this as any).value;
            (this as any).flag = true;
            (this as any).continueFlag = false;
            (this as any).restartFlag = true;
          }
          return;
      }
    },
    setDot() { //小数点を表示
      (this as any).value = String((this as any).value) + String(".");
      (this as any).showValue = (this as any).value;
    },
    setMinus() { //マイナスを表示
      if (String((this as any).value).indexOf("-") == -1) {
        (this as any).value = String("-") + String((this as any).value);
        (this as any).showValue = (this as any).value;
      } else if (String((this as any).value).indexOf("-") == 0) {
        (this as any).value = String((this as any).value).replace("-", "");
        (this as any).showValue = (this as any).value;
      }
    },
    repalacePercentage() { //パーセンテージで表示
      (this as any).value = (this as any).value / 100;
      (this as any).showValue = (this as any).value;
    },
    
    clearValue() { //クリアボタン
      (this as any).firstValue = 0;
      (this as any).value = 0;
      (this as any).showValue = (this as any).value;
      (this as any).flag = true;
    },
  },
};
</script>

<style>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  background-color: black;
}

.calculator {
  width: 100%;
  margin: 0 auto;
  max-width: 400px;
  height: 100%;
  background: black;
  padding: 8px;
  box-sizing: border-box; 
}

.button {
  user-select: none;
  border-radius: 50%;
  height: 76px;
  width: 76px;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 32px;
  margin: 0px 6px;
}

.button:hover {
  cursor: pointer;
}

.double {
  width: 164px !important;
  border-radius: 41px !important;
  justify-content: flex-start !important;
  padding: 0px 28px !important;
}

.button-column {
  display: flex;
  margin-bottom: 24px;
}

.calculation {
  background: #fe9f0a;
  color: white;
}

.number {
  background: #333333;
  color: white;
}

.other {
  background: #a5a5a5;
  color: black;
  font-size: 24px !important;
}

.calc-value {
  height: 200px;
  display: flex;
  justify-content: flex-end;
  align-items: flex-end;
  color: white;
  font-size: 88px;
  padding: 0px 20px;
  margin-right: 16px;
}
</style>