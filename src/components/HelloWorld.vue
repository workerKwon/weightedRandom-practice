<template>
  <div id="hello">
    <div class="file">
      <input class="file-btn" type="file" @change="readFile"/>
    </div>
    <div>
      <input class="input-num" v-model="realNum[0]"/>
      <input class="input-num" v-model="realNum[1]"/>
      <input class="input-num" v-model="realNum[2]"/>
      <input class="input-num" v-model="realNum[3]"/>
      <input class="input-num" v-model="realNum[4]"/>
      <input class="input-num" v-model="realNum[5]"/>
      <input class="input-num" v-model="realNum[6]"/>
      <button class="save-btn" @click="save">저장</button>
    </div>
    <div>
      <button class="ex-btn" @click="numbering">번호 뽑기</button>
    </div>
    <div class="one-line" v-for="(num, index) in extractedNum" v-bind:key="index">
      <div v-for="(n, i) in num" v-bind:key="i">
        <div class="six-num">{{n}}</div>
      </div>
    </div>
  </div>
</template>

<script>
  import XLSX from 'xlsx'
export default {
  name: 'HelloWorld',
  data(){
    return {
      animals: [],
      weightedArray: [],  // number 각 숫자의 weight만큼 개수를 늘리기 위해 사용.
      realNum: [],
      number : [],  // 역대 당첨 번호별로 weight와 함께 저장되는 변수.
      extractedNum : [],  // 확률 기반으로 내가 뽑아보는 당첨 번호.(결과)
    }
  },
  methods:{
    save(){ // 이번 당첨 번호 저장.
      for(var i = 0 ; i < this.realNum.length; i++){
        this.number.forEach((num) => {
          if(num.num == this.realNum[i]){  // 입력한 번호와 같은 번호의 비중을 늘린다.
            num.weight++
          }
        })
      }
      localStorage.setItem('weightedNum', JSON.stringify(this.number))
      this.setWeight();
      this.realNum = []
    },

    numbering(){ // 예상 번호 추출 함수
      let exNum = [];
        while (exNum.length <= 5) {  // exNum이 7개가 되면 멈춘다.
          var num = this.number[this.weightedArray[Math.floor(Math.random() * this.weightedArray.length)]].num
          if (!exNum.includes(num)) { // 중복되는 값이 있으면 저장하지 않는다.
            exNum.push(num)
          }
        }
        exNum.sort(function (a, b) { // 추출된 7개의 숫자를 오름차순 정렬.
          return a - b;
        })
        this.extractedNum.push(exNum) // 결과에 담는다.
    },

    setNum(){ // 파일을 다시 불러올 경우 this.number를 1 ~ 45까지 모두 weight를 0으로 초기화하는 함수
      this.number = [];
      var array = new Array(45);
      for(var i = 1 ; i <= array.length; i++) {
        this.number.push({num : i, weight : 0});
      }
    },

    setWeight(){  // 역대 당첨 번호 별로 각각 weight를 담아두는 함수.
      this.weightedArray = [];
      for(var j = 0 ; j < this.number.length; j++) {
        for (var n = 0; n < this.number[j].weight; n++) {
          this.weightedArray.push(j) // weight만큼 숫자의 개수를 만든다.
        }
      }
    },

    readFile(event){
      this.setNum();
      const file = event.target.files[0];

      const reader = new FileReader();  // input type file과 같이 쓰일 수 있으며, file을 binary string으로 변환한다.
      const tmpResult = {}

      // onload 함수와 readAs...() 함수가 비동기적으로 이루어져,
      // 비동기 처리를 해주던가 파일이 load되고 난 후에 data를 변환할 수 있는 onload 함수를 사용한다.
      reader.readAsArrayBuffer(file)
      // 파일이 완전히 업로드 됐을 때, 파일이 data로 변환되어 onload에 callback으로 전달된다.
      reader.onload = (e) => {
        let data = e.target.result;
        data = new Uint8Array(data)

        let excelFile = XLSX.read(data, { type: "array" })

        excelFile.SheetNames.forEach(function(sheetName) {
          const roa = XLSX.utils.sheet_to_json( excelFile.Sheets[sheetName], { header: 1 } );
          if (roa.length) tmpResult[sheetName] = roa;
        });
        const result = tmpResult.excel;
        result.forEach((index) => {
          index.forEach((number) => {
            for(var i = 0; i < this.number.length; i++){
              if(this.number[i].num == number){
                this.number[i].weight++
              }
            }
          })
        })
        localStorage.setItem('weightedNum', JSON.stringify(this.number))
        this.setWeight();
      }
    },
  },

  mounted(){ // 시작할 때 localStorage에 저장해놨던 weightedNum을 불러와서 this.number에 저장.
    const item = JSON.parse(localStorage.getItem('weightedNum'))
    this.number = item
    this.setWeight();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  #hello{
    display: flex;
    flex-direction: column;
    justify-content: center;
  }
  .file{
    display: flex;
    justify-content: center;
    margin-top: 50px;
  }
  .file-btn{
    height: 40px;
    outline: none;
  }
  .file-btn:active{
    background-color: aliceblue;
  }
  .input-num{
    width: 40px;
    height: 40px;
    margin: 10px;
    border-radius: 5px;
    text-align: center;
    font-size: 20px;
  }
  .input-num:focus{
    outline: none;
  }
  .one-line{
    display: flex;
    flex-direction: row;
    justify-content: center;
  }

  .six-num{
    margin: 10px;
  }

  .ex-btn{
    width: 80px;
    height: 50px;
    border-radius: 5px;
    font-size: 15px;
    outline: none;
    margin-bottom: 30px;
  }
  .ex-btn:active{
    background-color: beige;
  }

  .save-btn{
    width: 70px;
    height: 35px;
    border-radius: 5px;
    font-size: 15px;
    outline: none;
  }
  .save-btn:active{
    background-color: beige;
  }
</style>
