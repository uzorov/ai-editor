<script>


export default { 
  // Properties returned from data() become reactive state
  // and will be exposed on `this`.
  data() {
    return {
      color: 'green',
      count: 0,
      listOfNodes: [],
      listOfLayersNumber: [],
      
    }
  },

  // Methods are functions that mutate state and trigger updates.
  // They can be bound as event listeners in templates.
  methods: {
    
    addLayer(){
      this.count++,
      this.listOfLayersNumber.push(this.listOfLayersNumber.length+1)
     this.listOfNodes.push(0);
      
    },

    delLayer(){
      this.count--
      this.listOfLayersNumber.splice(-1);
      this.listOfNodes.splice(-1);
      
    },

    increaseNodes(item){
       this.listOfNodes[item-1]++
       this.addLayer()
       this.delLayer()
    },

    decreaseNodes(item){
      if (this.listOfNodes[item-1]>0)
      this.listOfNodes[item-1]--;
      this.addLayer();
      this.delLayer();
    },
    getJSON(){
      var brain = require('brain.js');

      var net = new brain.NeuralNetwork({
    hiddenLayers: this.listOfNodes,
    learningRate: 0.6 // общая степень обученности, полезна при обучении в несколько потоков
});

net.train([{input: [0, 0], output: [0]},
           {input: [0, 1], output: [1]},
           {input: [1, 0], output: [1]},
           {input: [1, 1], output: [0]}]);

var output = net.run([1, 0]);  // [0.987]
console.log(output);
var jsonData = net.toJSON();

var dictstring = JSON.stringify(jsonData, null, " ");


    this.download(dictstring, 'json.txt', 'text/plain');

    },
    
    download(content, fileName, contentType) {
    var a = document.createElement("a");
    var file = new Blob([content], {type: contentType});
    a.href = URL.createObjectURL(file);
    a.download = fileName;
    a.click();
}
  },

  // Lifecycle hooks are called at different stages
  // of a component's lifecycle.
  // This function will be called when the component is mounted.
  mounted() {
    console.log(`The initial count is ${this.count}.`)
  }
}
</script>

<template>
  <v-app id="inspire">
   

    <v-app-bar app>
      <v-toolbar-title class = "mr-10 ml-5 font-weight-bold">Простой редактор параметров нейросети</v-toolbar-title>
      <v-btn class = "green lighten-1 white--text ml-10" @click="addLayer" >
            Добавить скрытый слой
          </v-btn>
          <v-btn class = "ml-4 red lighten-2 white--text" @click="delLayer">
            Удалить слой
          </v-btn>

          <v-btn class="ml-4" @click="getJSON">
        Получить структуру нейросети в JSON
      </v-btn>

    </v-app-bar>

   
   
    <v-main class="grey lighten-2">
      <v-container>
        <v-row>
          <template v-for="n in count">
            <v-col
              :key="n"
              class="mt-2"
              cols="12"
            >

            <v-col
          
            cols = "auto"
            
            >
        
              <h1 class = "font-weight-light mr-4">Cлой {{ n }}</h1> 
              <v-btn class = "blue darken-3 white--text mr-2" @click="increaseNodes(n)">Добавить нейрон</v-btn>
              <v-btn class = "red lighten-2 white--text mr-2" @click="decreaseNodes(n)">Удалить</v-btn>
           
            </v-col>
            </v-col>

            <v-col
              v-for="j in listOfNodes[n-1]"
              :key="`${n}${j}`"
              cols="1"
              md="1"
            >
              <v-sheet class="font-weight-medium green lighten-2 white--text" width="70" height="70">{{ j }}</v-sheet>
            </v-col>
          </template>
        </v-row>
      </v-container>
<v-textfield>
  {{json}}
</v-textfield>
      
    </v-main>
  </v-app>
</template>



