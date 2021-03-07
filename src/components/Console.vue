<template>
    <div  class="console-container">
           <div >
         <div :style="message.color? 'color:'+message.color : 'color:gray;'" v-for="(message,index) in output" :key="index">{{message.text}} <br/></div>
           </div>
    </div>
</template>
<script>
export default {
    name:"Console",
    props:['output'],
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