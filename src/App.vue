<template>
  <div id="app">
    <div class="calculator">
      <div class="calculator__title">Calculator</div>
       <div class="calculator__score-wrapper">
          <input
          class="calculator__scoreboard"
          @keyup.enter="enterHandler"
          @input="errorHandler"
          type="text"
          :value="result">
          <div class="error-info">{{error}}</div>
       </div>
      <div class="calculator__btns">
          <div class="calculator__btn-top">
            <div class="calculator__btn" @click="clearScore">Clear</div>
            <div class="calculator__btn" @click="undo">Undo</div>
          </div>
          <div class="calculator__btn-bottom">
            <div class="calculator__btn"
              v-for="(item, index) in buttons"
              v-bind:key="index"
              @click="btnHandler">
              {{item}}
            </div>
          </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data: () => ({
    result: '',
    buttons: [1, 2, 3, '/', 4, 5, 6, '*', 7, 8, 9, '-', 0, ',', '=', '+'],
    error: '',
  }),
  methods: {
    enterHandler(e) {
      this.calc(e.target.value);
    },
    calc(value) {
      let str = value;
      const numbers = [];
      const singsOne = [];
      const singsTwo = [];
      let num = '';
      let isPrevNumber = false;
      if (str.charAt(0) === '-') {
        num = '-';
        // eslint-disable-next-line no-const-assign
        str = str.slice(1);
      }
      for (let i = 0; i < str.length; i += 1) {
        const char = str.charAt(i);
        // eslint-disable-next-line no-restricted-globals
        if (!isNaN(char)) {
          num += char;
          if (i === str.length - 1) {
            numbers.push(+(num));
          }
          isPrevNumber = true;
        } else if (char === '.' || char === ',') {
          if (i === str.length - 1) {
            numbers.push(+(num));
          } else num += '.';
          isPrevNumber = true;
        } else if (char === '/' || char === '*') {
          if (isPrevNumber) {
            const obj = { index: numbers.length, val: char };
            numbers.push(+(num));
            num = '';
            isPrevNumber = false;
            if (i !== str.length - 1) singsOne.push(obj);
          }
        } else if (char === '+' || char === '-') {
          if (isPrevNumber) {
            const obj = { index: numbers.length, val: char };
            numbers.push(+(num));
            num = '';
            isPrevNumber = false;
            if (i !== str.length - 1) singsTwo.push(obj);
          }
        } else {
          this.error = 'data is not valid';
        }
      }
      let i = 0;
      singsOne.forEach((elem) => {
        const val = elem.index - i;
        if (elem.val === '*') {
          // eslint-disable-next-line operator-assignment
          numbers[val] = numbers[val] * numbers[val + 1];
        } else {
          // eslint-disable-next-line operator-assignment
          numbers[val] = numbers[val] / numbers[val + 1];
        }
        numbers.splice(val + 1, 1);
        i += 1;
      });
      singsTwo.forEach((elem) => {
        if (elem.val === '+') {
          numbers[0] += numbers[1];
        } else {
          numbers[0] -= numbers[1];
        }
        numbers.splice(1, 1);
      });
      // eslint-disable-next-line no-restricted-globals
      if (this.error || isNaN(numbers[0])) {
        this.result = value;
        this.error = 'data is not valid';
      // eslint-disable-next-line prefer-destructuring
      } else this.result = numbers[0];
    },
    btnHandler(e) {
      this.cleanError();
      const table = document.querySelector('.calculator__scoreboard').value;
      let text = e.target.innerText;
      if (text === '=') this.calc(table);
      else {
        this.result = table;
        if (this.result === '0') this.result = '';
        this.result += text;
      }
      text = '';
    },
    clearScore() {
      this.cleanError();
      this.result = 0;
    },
    undo() {
      this.cleanError();
      this.result = this.result.toString().slice(0, -1);
    },
    errorHandler(e) {
      this.cleanError();
      this.result = e.target.value;
    },
    cleanError() {
      this.error = '';
    },
  },
};
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Open+Sans:wght@700&display=swap');
*{
  box-sizing: border-box;
}
body {
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #bbb;
}
.calculator{
  background: #80ff00;
  padding: 10px;
  font-size: 18px;
  border-radius: 2px;
  box-shadow: 0 0 2px rgb(88, 88, 88),
              0 0 4px rgb(88, 88, 88),
              0 0 8px rgb(88, 88, 88),
              0 0 20px rgb(88, 88, 88);
  &__title{
    margin-bottom: 10px;
    text-align: center;
    font-weight: 700;
    font-size: 24px;
    font-family: 'Open Sans', sans-serif;
  }
  &__scoreboard{
    width: 300px;
    padding: 8px 10px 15px;
    font-size: 20px;
    outline: none;
  }
  &__btns{
    margin-top: 10px;
  }
  &__btn-top{
    display: flex;
    justify-content: center;
  }
  &__btn-top div{
    width: 148px;
  }
  &__btn-bottom{
    display: flex;
    justify-content: center;
    align-items: center;
    flex-wrap: wrap;
    width: 300px;
  }
  &__btn{
    width: 73px;
    height: 30px;
    border: 1px solid black;
    text-align: center;
    line-height: 30px;
    margin: 1px;
    border-radius: 2px;
    cursor: pointer;
    transition: .5s;
  }
  &__btn:hover{
    background: #5c9b1d;
  }
}
.calculator__score-wrapper{
  position: relative;
}
.error-info{
  position: absolute;
  bottom: 0;
  padding-left: 10px;
  font-size: 14px;
}
</style>
