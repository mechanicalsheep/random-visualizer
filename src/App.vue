<template>
  <v-app>
    <v-app-bar
      app
      color="yellow"
      dark
      height="220"
    >
  
    <v-row style="padding-top:10px; width:100%;">
      <v-col :cols="12">
   <h2 style="color: black; text-align:center;">Random Visualizer</h2>
      </v-col>
    
    <v-row style="width:100%;" class="justify-center pt-3">
      <v-col cols="2">
        <div >
        <v-text-field v-model="numNodes" type="number" light color="black" label="Number of nodes"/>
        </div>
      </v-col>
    
      <v-col cols="2">
        <div >
        <v-text-field v-model="intervalSeconds" type="Number" light color="black" label="Speed (in seconds)"/>
        </div>
      </v-col>
    </v-row >
    <v-row style="width:100%;" class="justify-center">
      <v-col cols="auto" class="text-center">
      <v-btn @click="RunRandomizer()">Randomize!</v-btn>
      </v-col>
      <v-col cols="auto">
      <v-btn @click="StopRandomizer()">Stop</v-btn>
      </v-col>
      <v-col cols="auto">
      <v-btn @click="test()">Test</v-btn>
      </v-col>
     
    </v-row>
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
    },
    intervalSeconds:function(){
      this.StopRandomizer();
      this.RunRandomizer();
    }
  },
  data: () => ({

    intervalSeconds:2,
    numNodes:5,
    deadNodeIndices:[],
    aliveNodeIndices:[],
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
      this.nodeConfigs.push({index:i,counter:0,isActive:false, ratio:0, weight:0, disable:false})
      }

      this.nodeConfigs.map(node=>{
        node.weight= Math.random().toPrecision(1);
      })

    },
    RunRandomizer(){
      this.randomInterval= setInterval(this.ProcessRandomization,this.intervalSeconds*1000)
    },
    StopRandomizer(){
      clearInterval(this.randomInterval);
    },
    ProcessRandomization(){
        this.ChooseNode();
        this.RunDeathTrigger();
    },
    CalculateRatios(){
      this.nodeConfigs.forEach(config=>{
        config.ratio = (config.counter/this.globalCounter *100).toFixed(2);
      })
    },
    RunDeathTrigger(){
    if(this.ShouldDie(this.activeConfig)){
          this.Kill(this.activeConfig);
        }
    },
    ShouldDie(config){
      let random = Math.random();
      if(random <= config.weight){
        return true;
      }
      return false;
    },
    Kill(config){
      config.disable=true;
      this.deadNodeIndices.push(config.index);
      console.log(`Node ${config.index} just passed away mysteriously. RIP big guy.`)
    },
    GetRandomIndex(){
      this.aliveNodeIndices = this.nodeConfigs.filter(node=> !this.deadNodeIndices.includes(node.index)).map(node=> node.index);
      let randomResult = Math.floor(Math.random() * Math.floor(this.aliveNodeIndices.length));
        
      return randomResult;
    },
    ChooseNode(){
       let randomResult =  this.GetRandomIndex();
        if(this.activeConfig){
          this.activeConfig.isActive=false;
        }
        this.globalCounter++;
        this.activeConfig = this.nodeConfigs[this.aliveNodeIndices[randomResult]];
        this.activeConfig.counter++;
        this.activeConfig.isActive=true;
        this.CalculateRatios();
    }
  
    
  }
}
</script>

<style>

</style>
