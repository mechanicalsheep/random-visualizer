<template>
    <v-container style="max-width:fit-content;">
        <v-card width="80px" height="100px" color="yellow">
            <v-card-title class="justify-center">
                <div class="status "></div>
            </v-card-title>
            <v-card-text class="text-center">
                <h3 style="font-weight:bolder;">{{index}}</h3>

                </v-card-text>
                <v-card-actions class="justify-center">
                    <h2>{{counter}}</h2>
                </v-card-actions>
        </v-card>
    </v-container>
</template>

<script>
export default {
    name:"Node",
    data(){
        return{
            isActive:false,
            index:1,
            counter:15
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