<script>

export default {
  data() {
    return {
      table: [], //эту таблицу мы генерируем в самом начале и ее мы будем изменять, когда пользователь двигает ячейки
      correctTable: [], //верная таблица, которая должна получиться в случае победы. С ней мы сравниваем главную таблицу, чтобы определить победил ли пользователь
      clickCounter:0, //счетчик ходов
      defaultSize: 4, //размер таблицы по дефолту
      size: 4, //текущий размер таблицы
      row: [],
      time: new Date(2011, 0, 1, 0, 0, 0, 0), //создаем экземпляр даты для таймера. Дата рандомная, нам нужны только секунды, минуты и часы
      seconds: 0, //Сюда мы будем записывать секунды, минуты и часы и следить за их изменениями. Это нужно чтобы вывести их в формате 00:00:00
      minutes: 0,
      hours: 0,
      sec: '00', //стартовое время
      min: '00',
      h: '00',
      gameStatus: false, //отслеживаем идет игра или нет
      timerInt: '',
      Iswin: '' //переменная победы
    }
  },
  methods: {
    //функция генерирует поле заданного размера
    generate(sizeTable) {
      this.clickCounter = 0 //сброс кликов
      this.Iswin = false //сброс победы
      this.sec ='00' //сброс времени
      this.min ='00'
      this.h ='00'
      clearInterval(this.timerInt) //сброс таймера
      this.time = new Date(2011, 0, 1, 0, 0, 0, 0)
      this.gameStatus = false
      this.size = sizeTable
      //очищаем таблицу которую двигаем и таблицу которая должна получиться
      this.correctTable = []
      this.table = []
      let count = 0
      let num = sizeTable
      //цикл для заполнения двух таблиц. Мы заполняем две таблицы параллельно, потому что не можем их приравнять. Если мы ставим "=" между двумя массивами, то копируется ссылка на этот массив
      //то есть если мы будем изменять одну таблицу, будет меняться и другая
      for (let i = 0; i < sizeTable; i++) {
        //это строки для главной таблицы и верной таблицей
        this.row = []
        const correctRow = []
        //цикл который заполняет строки которые создали выше
        for (let k = count; k < num; k++) {
          this.row.push(k + 1)
          correctRow.push(k + 1)
        }
        count += sizeTable
        num += sizeTable
        //заполнение итоговых таблиц
        this.table.push(this.row)
        this.correctTable.push(correctRow)
      }
      this.table[sizeTable - 1][sizeTable - 1] = 0 //последнее число в таблице заменяем на "0", то есть пустая ячейка
      this.correctTable[sizeTable - 1][sizeTable - 1] = 0 //последнее число в таблице заменяем на "0", то есть пустая ячейка
      console.log(this.table)
      console.log(this.correctTable)
    },
    //функция генерирует случайное число 0-3
    getRandom() {
      let number = Math.round(3 * Math.random())
      return number
    },
    //функция перемешивает поле. Ищет ячейку с 0 значением, получает рандомное число и, основываясь на нем, меняет 0 на соседню ячейку(вправо - 0, влево - 1, вниз - 2, вверх - 3)
    //также проверяет возможна ли замена (ячейки может не существовать) 
    reset(size) {
      this.Iswin = false
      this.gameStatus = true //начало игры
      this.sec = '00', //очищаем время, которое осталось с прошлой игры
      this.min = '00',
      this.h = '00'
      this.clickCounter = 0
      clearInterval(this.timerInt)
      this.time = new Date(2011, 0, 1, 0, 0, 0, 0)
      this.startTimer() //запускаем таймер
      //проходит 100 раз по всей таблице и делает 100 замен (чем больше число, тем сильнее перемешает)
      for (let k = 0; k < 10; k++) {
        //два цикла чтоб пройтись по таблице
        for(let i = 0; i < size; i++) {
          for(let j = 0; j < size; j++) {
            //проверка, является ли ячейка 0
            if (this.table[i][j] == 0) {
              //получаем рандомное число 0-3
              let number = this.getRandom()
              //если получили 0 двигаем направо
              if (number == 0) {
                //проверка, существует ли ячейка
                try {
                  if (!(typeof this.table[i][j+1] == 'undefined')) {
                    console.log('1')
                    //меняем значения ячеек местами
                    let number = this.table[i][j+1]
                    this.table[i].splice(j+1, 1, this.table[i][j])
                    this.table[i].splice(j, 1, number) 
                    console.log(this.table[i][j])               
                  }
                } catch(e) {}
              }
              //если получили 1 двигаем налево
              if (number == 1) {
                //проверка, существует ли ячейка
                try {
                  if (!(typeof this.table[i][j-1] == 'undefined')) {
                    console.log('2')
                    //меняем значения ячеек местами
                    let number = this.table[i][j-1]
                    this.table[i].splice(j-1, 1, this.table[i][j])
                    this.table[i].splice(j, 1, number)
                    console.log(this.table[i][j]) 
                  }
                } catch(e) {}               
              }
              //если получили 2 двигаем вниз
              if (number == 2) {
                //проверка, существует ли ячейка
                try {
                  if (!(typeof this.table[i+1][j] == 'undefined')) {
                    console.log('3')
                    //меняем значения ячеек местами
                    let number = this.table[i+1][j]
                    this.table[i+1].splice(j, 1, this.table[i][j])
                    this.table[i].splice(j, 1, number)
                    console.log(this.table[i][j])
                  }
                } catch(e) {}                
              }
              //если получили 3 двигаем вверх
              if (number == 3) {
                //проверка, существует ли ячейка
                try {
                  if (!(typeof this.table[i-1][j] == 'undefined')) {
                    //меняем значения ячеек местами
                    console.log('4')
                    let number = this.table[i-1][j]
                    this.table[i-1].splice(j, 1, this.table[i][j])
                    this.table[i].splice(j, 1, number)
                    console.log(this.table[i][j])
                  }
                } catch(e) {}
              }
            }
          }     
        }
      }
      console.log(this.table)
    },
    //функция которая меняет числа в таблице, то есть перемещения ячеек. Принимает координаты ячейки, которую двигаем на пустое место. 
    //Сначала проверяем с какой стороны находится пустота, а после меняем их значения местами
    move(i, j, view)
    {
      try {
      //если двигаем вниз
        if (!(typeof this.table[i+1][j] === 'undefined')) {
          if (this.table[i+1][j] == 0)
          {
            //меняем значения ячеек местами
              let number = this.table[i+1][j]
              this.table[i+1][j] = this.table[i][j]
              this.table[i][j] = number
              //увеличиваем кол-во ходов
              this.clickCounter++
              //проверяем, выиграл ли пользователь
              this.isComplete(this.correctTable)
          }
        }
      }
      catch(e) {}
      try {
        //если двигаем вверх
        if (!(typeof this.table[i-1][j] === 'undefined')) {
          if (this.table[i-1][j] == 0) {
            let number = this.table[i-1][j]
            this.table[i-1][j] = this.table[i][j]
            this.table[i][j] = number
            this.clickCounter++
            this.isComplete(this.correctTable)
          }
        }
      } catch(e) {}
      //если двигаем вправо
      try {
        if (!(typeof this.table[i][j+1] === 'undefined')) {
          if (this.table[i][j+1] == 0)
          {
            let number = this.table[i][j+1]
            this.table[i][j+1] = this.table[i][j]
            this.table[i][j] = number
            this.clickCounter++
            this.isComplete(this.correctTable)
          }
        }
      } catch(e) {console.log(e)}
      //если двигаем влево
      try {
        if (!(typeof this.table[i][j-1] === 'undefined'))
        {
          if (this.table[i][j-1] == 0)
          {
            let number = this.table[i][j-1]
            this.table[i][j-1] = this.table[i][j]
            this.table[i][j] = number
            this.clickCounter++
            this.isComplete(this.correctTable)
          }
        }
      } catch(e) { console.log(e) }     
    },
    //функция проверят выиграл ли пользователь, то есть сравнивает две таблицы - главную, которую мы двигаем и верную
    isComplete(table) {
      console.log(table.length)
      //чтобы сравнить две таблицы (массива) мы их конвертируем в JSON строку и после сравниваем. Просто "=" мы поставить не можем. Если таблицы совпали то true, иначе false
      //true = победа
      let status = JSON.stringify(this.table) === JSON.stringify(table)
      if (status) {
        this.Iswin = true
        this.gameStatus = false
        this.time = new Date(2011, 0, 1, 0, 0, 0, 0)
        clearInterval(this.timerInt)
      }
    },
    //функция таймера
    startTimer() {
      //создаем системный таймер, который выполняет функцию раз в секунду
      this.timerInt = setInterval(() => {
        let seconds = this.time.getSeconds() //получаем секунды из нашего экземпляра даты
        seconds += 1 //прибавляем секунду
        this.time.setSeconds(seconds) //записываем в экземпляр даты
        this.seconds = this.time.getSeconds()  //также получаем секунды, минуты и часы за которыми следим (в коде ниже)
        this.minutes = this.time.getMinutes()
        this.hours = this.time.getHours()
      }, 1000)
    },
    
  },
  //объект в которых мы создаем функции с названиями переменных, объявленные заранее. Функция срабатывает, как только переменная изменилась, то есть следим за ней
  watch: {
    seconds() {
      //если секунды меньше 10, то выводим их в формате 01, 02, 03 и так далее. По дефолту оно выводится как 0, 1, 2
      //все тоже самое ниже для минут и часов
      if (this.seconds < 10) {
        this.sec = '0' + this.seconds
      } else {
        this.sec = this.seconds 
      }
    },
    minutes() {
      if (this.minutes < 10) {
        this.min = '0' + this.minutes
      } else {
        this.min = this.minutes 
      }
    },
    hours() {
      if (this.hours < 10) {
        this.h = '0' + this.hours
      } else {
        this.h = this.hours 
      }
    }
  },
  created() {
    //эта функция выполнится при создании Vue приложения, до отрисовки. По дефолту создается таблица 4 на 4
    this.generate(this.defaultSize)
  }
}
</script>

<template>
  <page>
    <ActionBar class="Bar">
        <Label text="Пятнашки" style="color: black; font-weight: bold;"/>
    </ActionBar>
    <FlexboxLayout class="main" flexDirection="column">
      <FlexboxLayout class="controlButtons" orientation="horizontal">
        <button class="button" :class="{'activeButton': size == 3}" @tap="generate(3)">3x3</button>
        <button class="button" :class="{'activeButton': size == 4}" @tap="generate(4)">4x4</button>
        <button class="button" :class="{'activeButton': size == 5}" @tap="generate(5)">5x5</button>
      </FlexboxLayout>
      <FlexboxLayout class="field" flexDirection="column">
        <StackLayout style="background-color: #ececf0; border-radius: 20px; vertical-align: middle;">
          <FlexboxLayout v-for="(row, i) in table" :key="i" class="flex1">
            <FlexboxLayout v-for="(item, j) in row" :key ="j" @tap="move(i, j, $event)" :class="{'empty': item == 0}" class="flex2">
              <label v-if="item != 0" style="font-size: 20px; margin: 8px; font-weight: bold;">{{ item }}</label>
              <label v-else style="font-size: 20px; margin: 8px; font-weight: bold"></label>
            </FlexboxLayout>
          </FlexboxLayout>
        </StackLayout>
        <label style="font-size: 20px; font-weight: bold; background-color: #3a4244; border-radius: 20px; padding: 2%,2%,2%,2%; margin-top: 10%;" v-if="Iswin">Победа!</label>
      </FlexboxLayout>
      <FlexboxLayout class="info">
        <label class="movesLabel">Ход: {{clickCounter}}</label>
        <Label class="timeLabel">Время: {{ h + ':' + min + ':' + sec }}</Label>
      </FlexboxLayout>
      <button @tap="reset(size)" class="startGame">Начать игру</button>
    </FlexboxLayout>
  </page>
</template>

<style scoped>
.main
{
  width: 100%;
  height: 100%;
  justify-content: space-between;
  background-color: aliceblue;
}
.controlButtons
{
  padding-top: 10%;
  width: 100%;
  justify-content: space-between;
  height: 12%;
}
.button
{
  font-size: 22px;
  font-weight: bold;
  border-radius: 20px; 
  background-color: #b6b6a7;
  width: 200px;
}
.field
{
  opacity: 0.5;
  width: 100%;
  height: 70%;
  align-items: center;
  padding-top: 70%;
}
.flex1
{
  margin-left: auto;
  margin-right: auto;
  margin-top: 1%;
  margin-bottom: 1%;
}
.flex2
{
  margin-left: 1.5%;
  margin-right: 1.5%;
  width: 175px;
  height: 175px;
  background-color: #b6b6a7;
  justify-content: space-around;
  text-align: center;
  border-color: #3a4244;
  border-width: 2;
  box-sizing: border-box;
  border-radius: 20px;
}
.info
{
  justify-content: space-around;
}
.Bar
{
  background-color: aliceblue;
}
.movesLabel
{
  font-size: 20px; 
  margin: 8px; 
  text-align: center; 
  background-color:  #cd9982; 
  border-radius: 20px; 
  width: 300px;
  font-weight: bold;
}
.timeLabel
{
  width: 500px;
  font-size: 20px; 
  margin: 8px; 
  text-align: center; 
  background-color:  #cd9982; 
  border-radius: 20px;
  font-weight: bold;
}
.startGame
{
  border-radius: 20px; 
  background-color: #cd9982;
  font-weight: bold;
  font-size: 25px;
  margin-bottom: 2.5%;
}
.activeButton
{
  font-size: 25px;
  font-weight: bold;
  border-radius: 20px; 
  width: 200px;
  background-color: #cd9982;
}
.empty
{
  opacity: 0;
  border-color: #ececf0;
  background-color: #ececf0;
}
</style>
