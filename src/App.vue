<template>
  <v-app>
    <v-app-bar
      app
      color="yellow"
      dark
      height="280"
    >
  
    <v-row style="padding-top:10px;">
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
    <v-row style="width:100%;" justify="center">
      <v-col cols="2">
        <label style="color:black; font-size:larger;">Current Amount: <b style="  font-weight:bolder;">{{money}}</b></label>
      </v-col>
      <v-col cols="2">
      <v-text-field light label="bet" v-model="betAmount"/>
      </v-col>
    </v-row>
    <v-row style="width:100%;" class="justify-center">
      <v-col cols="auto" class="text-center">
      <v-btn @click="RunRandomizer()">Randomize!</v-btn>
      </v-col>
      <v-col cols="auto">
      <v-btn @click="StopRandomizer()">Stop</v-btn>
      </v-col>
      <v-col cols="auto">
      <v-btn @click="PlayAgain()">Play Again</v-btn>
      </v-col>
      <v-col cols="auto">
      <v-btn @click="Reset()">Reset Game</v-btn>
      </v-col>
     
    </v-row>
    </v-row>
   
   <v-row>
     <v-col>
     <Console :output="output" v-on:clear-output="ClearOutput()"/>
     </v-col>
   </v-row>
    </v-app-bar>

    <v-main>
        <v-row  v-if="Number.parseInt(numNodes)>0">
          <v-col 
          style="width:fit-content; max-width:fit-content; margin:auto;" 
          :style="selectedNode===nodeConfig? 'background-color:green':''" 
          v-for="(nodeConfig, idx) in nodeConfigs" :key="idx"
          @click="SelectBettingNode(nodeConfig)">
            <Node  :nodeConfig="nodeConfig"/>
          </v-col>
        </v-row>
    </v-main>
  </v-app>
</template>

<script>
import Node from './components/Node'
import Console from './components/Console'

export default {
  name: 'App',

  components: {
    Node,
    Console
  },
  created(){
   this.InitializeNodeConfig();
  },
  watch:{
    numNodes:function(){
     this.InitializeNodeConfig();
    },
    intervalSeconds:function(){
      this.StopRandomizer();
      this.RunRandomizer();
    }
  },
  computed:{
    aliveNodeIndices : function(){
     return this.nodeConfigs.length>0? this.nodeConfigs.filter(node=> !this.deadNodeIndices.includes(node.index)).map(node=> node.index) : 0
    }
  },
  data: () => ({

    selectedNode:'',
    money:10,
    betAmount:0,
    output:[],
    intervalSeconds:2,
    numNodes:5,
    deadNodeIndices:[],
    randomization:'',
    nodeConfigs:[],
    randomizationRunning:false,
    randomInterval:'',
    activeConfig:'',
    globalCounter:0,
  }),
  methods:{
    Reset(){
      this.PlayAgain();
      this.money=10;
    },
    PlayAgain(){
      if(this.randomizationRunning){
        this.StopRandomizer();
      }
      this.ClearOutput();
        this.InitializeNodeConfig();
    },
    ClearOutput(){
      this.output=[];
    },
    InitializeNodeConfig(){
      this.nodeConfigs=[];
      this.selectedNode='';
      this.deadNodeIndices=[];
      for(let i=0; i < this.numNodes; i++){
        this.nodeConfigs.push({index:i,counter:0,isActive:false, ratio:0, weight:0, disable:false})
      }

      this.nodeConfigs.map(node=>{
        node.weight= Math.random().toPrecision(1);
      })
        this.output.push({color:'green',text:'initialized.'})

    },
    RunRandomizer(){
      try{
        if(this.ValidateBet()){
          this.randomizationRunning = true;
          this.output.push({color:'green', text:"Running Randomizer..."});
          this.randomInterval= setInterval(this.ProcessRandomization,this.intervalSeconds*1000)
        }
        
      }
      catch(e){
        this.StopRandomizer();
        console.log(e);
        
      }
    },
    StopRandomizer(){
      if(this.randomizationRunning){

        clearInterval(this.randomInterval);
        this.randomizationRunning=false;
        this.output.push({color:'red', text:"Randomizer Stopped"});
      }
    },
    SelectBettingNode(node){
      this.selectedNode = node;
      this.output.push({text:'selecting '+node.index})
    },
    ProcessRandomization(){
      try{

        //if this is the last node, start the endgame process.
        if(this.IsLastNodeAlive()){
          this.RunEndGameProcess();
        }
        //check if all nodes are dead, stop the randomizer if they are.
        else if(this.deadNodeIndices.length === this.nodeConfigs.length){
          this.StopRandomizer();
        }
        else{
          //if there was a previous active node then it is no longer active.
          if(this.activeConfig){
            this.activeConfig.isActive=false;
            }
            this.activeConfig =this.ChooseRandomNode();
            this.globalCounter++;
            this.activeConfig.counter++;
            this.activeConfig.isActive=true;
            this.CalculateRatios();
            this.RunDeathTrigger();
        }
      }
      catch(e){
        this.StopRandomizer();
        this.output.push({color:'red',text:'Error occured: '+e})
      }
      
    },
    CalculateRatios(){
      this.aliveNodeIndices.forEach(index=>{
        this.nodeConfigs[index].ratio = (this.nodeConfigs[index].counter/this.globalCounter *100).toFixed(2);
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
      this.output.push({text:`Node ${config.index} just passed away mysteriously. RIP big guy.`})
    },
    ChooseRandomNode(){
      let randomResult = Math.floor(Math.random() * Math.floor(this.aliveNodeIndices.length));
      
      return this.nodeConfigs[this.aliveNodeIndices[randomResult]];
    },
    IsLastNodeAlive(){
      return this.aliveNodeIndices.length===1;
    },
    IsSelectedNodeDead(){
      return this.deadNodeIndices.includes(this.selectedNode.index);
    },
    RunEndGameProcess(){
      
      this.StopRandomizer();

      let amountWon = this.betAmount*2;
      if(!this.selectedNode){
        this.output.push({color:'orange', text:'Those who do not venture, win nothing.'})
      }
      else if(!this.IsSelectedNodeDead()){
       this.output.push({color:"green", text:"Congratulations! You won "+amountWon+" moneys!"})
        this.money += amountWon;
      }
      else{
        let amountLost = Math.min(this.betAmount,this.money)
        this.output.push({color:"darkred", text:"You lost "+amountLost+" moneys, better luck next time."})
        this.money -= amountLost;
        if(this.money<0){
          this.money=0;
        }
      }
      
    },
    ValidateBet(){
      if(this.betAmount > this.money){
        this.output.push({color:'red',text:'Cannot place a bet larger than the money you have!'})
        return false;
      }
      return true;
    }
  
    
  }
}
</script>

<style>

</style>
