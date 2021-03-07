<template>
<v-container>
        <v-btn @click="Clear()">Clear</v-btn>
    <div  class="console-container">
           <div >
         <div :style="message.color? 'color:'+message.color : 'color:gray;'" v-for="(message,index) in output" :key="index">{{message.text}} <br/></div>
           </div>
    </div>
</v-container>
</template>
<script>
export default {
    name:"Console",
    props:['output'],
    
    methods:{
        Clear(){
            this.$emit('clear-output')
        }
    }
}
</script>
<style >
.console-container{
    width:500px;
    height:150px;
    overflow-y: scroll;
    overscroll-behavior-y: contain;
    scroll-snap-type: y proximity;
    background-color:white;
    scroll-snap-align: end;
}
.console-container >div> div:last-child{
    background-color:lightgray;
    scroll-snap-align: end;
}
</style>