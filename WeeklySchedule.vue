<template>
  <v-app>
    <div id="SS">
    <h1 class="text-center" >WeeklySchedule</h1>
  <v-container align="center">
    <v-row
        no-gutters
        v-for="h in 13"
        :key="h"
        rows="12"
        sm="4"
      >
      <v-col
      style="width:145px"
        v-for="d in 8"
        :key="d"
        cols="12"
        md="auto"
      >
      <v-card
        class="rounded-0 "
        :color="(d === 1 || h === 1)? '#3ecfbe' : '#FFF'"
        @click="options(h,d)"
        elevation="2"
        height="65px"
        width="500px"
      >
        <v-card-text
        class=text-center v-model=activities[d][h]>
              {{showDays(h,d)}}
        </v-card-text>
        
      </v-card>
      
      </v-col>
    </v-row>
  </v-container>
  </div>
  <v-container>
    <v-card
      class="rounded-0 "
    >
      <v-card 
      color="#3ecfbe"
      class="rounded-0 "
      >
        <v-card-title >Actions</v-card-title>
      </v-card>
      
      <v-card-text>Selected Time:  {{days[sDay-1]}} {{hours[sHour-1]}}</v-card-text>
      <v-card-actions>
        <v-btn
          color = #3ecfbe
          @click="toggleActivity"
        >
          <v-icon>mdi-plus</v-icon>Add Activity
        </v-btn>
        <v-btn
          color = #3ecfbe
          @click="isItDynamic()"
        >
          <v-icon>mdi-plus</v-icon>Reserve As Movie Time
        </v-btn>


        <v-btn
          color = #3ecfbe
          @click="injectRandom(sHour,sDay)"
        >
          <v-icon>mdi-plus</v-icon>Random Activity
        </v-btn>

      </v-card-actions>
      <v-card  v-if="custom" class="rounded-0 ">
        <v-card-title>Add Activity</v-card-title>
        <v-text-field hint="Enter the activity." autofocus=true v-if="custom" v-model="activitySlot"></v-text-field>
        <v-card-actions>
          <v-btn>
            Beni Böyle Üzmek Güzel mi
          </v-btn>
          <v-btn>
            Seni Seven Kalbim Küser mi
          </v-btn>
        </v-card-actions>
      </v-card>
      
    
    </v-card>
  </v-container>
  <v-btn @click="takeSS()">
            Take Screenshot
  </v-btn>
  <div id="output"></div>
  </v-app>
</template>

<script>
  import axios from "axios"
  import html2canvas from "html2canvas"

  export default {
    name: 'WeeklySchedule',

    data: () => ({
      days:["","Monday","Tuesday","Wednesday","Thursday","Friday","Saturday","Sunday"],
      hours:["","8.00","9.00","10.00","11.00","12.00","13.00","14.00","15.00","16.00","17.00","18.00","19.00"],
      activities:{0:{0:"",1:"",2:"",3:"",4:"",5:"",6:"",7:"",8:"",9:"",10:"",11:"",12:"",13:""},
                  1:{0:"",1:"",2:"",3:"",4:"",5:"",6:"",7:"",8:"",9:"",10:"",11:"",12:"",13:""},
                  2:{0:"",1:"",2:"",3:"",4:"",5:"",6:"",7:"",8:"",9:"",10:"",11:"",12:"",13:""},
                  3:{0:"",1:"",2:"",3:"",4:"",5:"",6:"",7:"",8:"",9:"",10:"",11:"",12:"",13:""},
                  4:{0:"",1:"",2:"",3:"",4:"",5:"",6:"",7:"",8:"",9:"",10:"",11:"",12:"",13:""},
                  5:{0:"",1:"",2:"",3:"",4:"",5:"",6:"",7:"",8:"",9:"",10:"",11:"",12:"",13:""},
                  6:{0:"",1:"",2:"",3:"",4:"",5:"",6:"",7:"",8:"",9:"",10:"",11:"",12:"",13:""},
                  7:{0:"",1:"",2:"",3:"",4:"",5:"",6:"",7:"",8:"",9:"",10:"",11:"",12:"",13:""},
                  8:{0:"",1:"",2:"",3:"",4:"",5:"",6:"",7:"",8:"",9:"",10:"",11:"",12:"",13:""}},
      sDay:0,
      sHour:0,
      custom:false,
      activitySlot:"",
      randomActivity:"",
    }),
    methods:{
      showDays: function(hour,day){
        if(hour === 1){
          return this.days[day-1]
        } 
        else if(day === 1){
          return this.hours[hour-1]
        }
        else{
          if(day === 1 && hour === 1){
          return ""
          }
          return this.activities[day-1][hour-1];
        }
      },
      options: function(hour,day){
        this.sHour = hour;
        this.sDay = day;
        console.log(day,hour)
      },
      toggleActivity: function(hour,day){
        this.custom = true;
        console.log("hour =", hour, "day = ", day)
      },
      injectRandom: function(h,d){
        axios.get('https://www.boredapi.com/api/activity/').then((response) => {
            this.activities[d-1][h-1] = response.data.activity;
        });
      },
      isItDynamic: function(){
        this.activities[5][5] = "xyz"
      },
      takeSS: function(){
            let div =
                document.getElementById('SS');
            html2canvas(div).then(
                function (canvas) {
                    document
                    .getElementById('output')
                    .appendChild(canvas);
                    console.log(canvas)
                })
        
      }
    },
  }
</script>

<style scoped>

</style>