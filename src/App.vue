<template>
  <v-app>
    <v-app-bar
      app
      color="yellow"
      dark
      height="100%"
    >
  
    <v-row style="padding-top:10px;">
      <v-col :cols="12">
    <h2 style="color: black; text-align:center;">Random Visualizer</h2>
      </v-col>
    
    
      <v-col cols="6">
        <v-text-field v-model="numNodes" light color="black" label="Number of nodes"/>
      </v-col>
    
    <v-col cols="6">
        <v-text-field v-model="randomization" light color="black" label="Randomization"/>
      </v-col>
    </v-row>
 
    </v-app-bar>

    <v-main>
      <v-container>
        <div style="max-width:fit-content" v-for="index in numNodes" :key="index">
         
      <Node/>
        </div>
      </v-container>
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

  data: () => ({
    numNodes:5,
    randomization:''
  }),
};
</script>

<style>

</style>
