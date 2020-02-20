<template>
  <div id="hello">
    <div id="rollOutput">

    </div>
    <input id="rollNumber" value="totalRolls"/>
    <button id="reRollBtn" @click="weightedRoll">Re Roll</button>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data(){
    return {
      animals: [],
      weightedArray: [],
      totalRolls: 100,
    }
  },
  methods:{
    weightedRoll(){
      var rollOutput = document.getElementById('rollOutput')
      var i;
      this.animals = [
        { name: "cat", weight: 5, count: 0 },
        { name: "dog", weight: 2, count: 0 },
        { name: "rabbit", weight: 2, count: 0 },
        { name: "mouse", weight: 1, count: 0 }
      ];
      this.weightedArray = [];
      for(i = 0 ; i < this.animals.length; i++){
        for(var n = 0; n < this.animals[i].weight; n++){
          this.weightedArray.push(i)
        }
      }

      for(i = 0; i < this.totalRolls; i++){
        this.animals[this.weightedArray[Math.floor(Math.random() * this.weightedArray.length)]].count++
      }
      rollOutput.innerHTML = ""
      for(i = 0 ; i < this.animals.length; i++){
        var newP = document.createElement('p')
        var newContent = document.createTextNode(
                this.animals[i].name +
                ": Weight: " +
                this.animals[i].weight +
                " - Count: " +
                this.animals[i].count +
                "/" +
                this.totalRolls +
                " (" + Math.round((this.animals[i].count / this.totalRolls) * 100) + "%)"
        );
        newP.appendChild(newContent)
        rollOutput.appendChild(newP)
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
