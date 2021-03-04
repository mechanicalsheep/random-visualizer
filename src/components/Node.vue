<template>
    <v-container style="max-width:fit-content; height:fit-content">
        <v-card width="80px" height="100px" :color="nodeConfig.disable? 'red': 'yellow'">
            <v-card-title class="justify-center">
                <div class="status " :class="nodeConfig.isActive? 'active':''"></div>
            </v-card-title>
            <v-card-text class="text-center">
                <h3 style="font-weight:bolder;">{{nodeConfig.index+1}}</h3>
                <h4>{{nodeConfig.ratio}}%</h4>
                </v-card-text>

        </v-card>
        <v-row class="justify-center">
            <v-col cols="auto">
                <h2>{{nodeConfig.counter}}</h2>
            </v-col>
            <v-col cols="12">
                <h5>Chance of death: {{nodeConfig.weight*100}}%</h5>
            </v-col>
        </v-row>
        <v-row>
        </v-row>
    </v-container>
</template>

<script>
export default {
    name:"Node",
    props:['nodeConfig'],

    data(){
        return{

        }
    }
}
</script>

<style scoped>
.status{
    border-radius: 50%;
width: 25px;
height:25px;
border: 1px solid black;
background-color:lightgray;
text-align: center;
/* fill: green; */
}
.active {
    background-color: green;
}
</style>
