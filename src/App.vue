<template>
  <v-app>
    <v-app-bar
      app
      color="yellow"
      dark
      height="200"
    >
  
    <v-row style="padding-top:10px; width:100%;">
      <v-col :cols="12">
   <h2 style="color: black; text-align:center;">Random Visualizer</h2>
      </v-col>
    
    
      <v-col cols="6">
        <v-text-field v-model="numNodes" type="number" light color="black" label="Number of nodes"/>
      </v-col>
    
      <v-col cols="6">
        <v-text-field v-model="randomization" light color="black" label="Randomization"/>
      </v-col>
    
      <v-col cols="12" class="text-center">
      <v-btn @click="RunRandomizer()">Randomize!</v-btn>
      <v-btn @click="StopRandomizer()">Stop</v-btn>
      </v-col>
     
    </v-row>
   
    </v-app-bar>

    <v-main>
        <v-row  v-if="Number.parseInt(numNodes)>0">
          <v-col style="width:fit-content; max-width:fit-content; margin:auto;" v-for="(nodeConfig, idx) in nodeConfigs" :key="idx">
            <Node :nodeConfig="nodeConfig"/>
          </v-col>
        </v-row>
    </v-main>
  </v-app>
</template>

<script>
import Node from './components/Node'

export default {
  name: 'App',

  components: {
    Node
  },
  created(){
   this.InitializeNodeConfig();
  },
  watch:{
    numNodes:function(){
     this.InitializeNodeConfig();
    },
    randomizationRunning:function(value){
      if(value){
        console.log(true);
      }
    }
  },
  data: () => ({
    numNodes:5,
    randomization:'',
    nodeConfigs:[],
    randomizationRunning:false,
    randomInterval:'',
    activeConfig:'',
    globalCounter:0,
  }),
  methods:{
    InitializeNodeConfig(){
      this.nodeConfigs=[];
      for(let i=0; i < this.numNodes; i++){
      this.nodeConfigs.push({index:i+1,counter:0,isActive:false, ratio:0})
      }
    },
    RunRandomizer(){
      console.log("Randomizer Running")
     
     let self = this;
       this.randomInterval= setInterval(function(){
        let randomResult =  self.Randomize();
        if(self.activeConfig){
          self.activeConfig.isActive=false;
        }
        self.globalCounter++;
        self.activeConfig = self.nodeConfigs[randomResult];
        self.activeConfig.counter++;
        self.activeConfig.isActive=true;
        self.CalculateRatios();
        // self.activeConfig.ratio= self.activeConfig.counter/ self.globalCounter *100;
        
       },2000)
       
     
    },
    StopRandomizer(){
      clearInterval(this.randomInterval);
    },
    Randomize(){
      let result = Math.floor(Math.random() * Math.floor(this.numNodes));
      console.log(result);
      return result;
    },
    CalculateRatios(){
      this.nodeConfigs.forEach(config=>{
        config.ratio = (config.counter/this.globalCounter *100).toFixed(2);
      })
    }
  
    
  }
}
</script>

<style>

</style>
