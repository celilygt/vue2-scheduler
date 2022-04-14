<template>
  <v-app>
    <div id="SS">
    <v-card color=#3ecfbe class="d-flex align-center justify-center pa-4 mx-auto"  >
      <v-card-title :style='{"font-size": 100}' >WeeklySchedule</v-card-title></v-card>
  <v-container justify="center">
    <v-row
        no-gutters
        v-for="h in 13"
        :key="h"
        rows="12"
        sm="4"
      >
      <v-col
        v-for="d in 8"
        :key="d"
      >
      <v-card
        class="rounded-0"
        :color="(d === 1 || h === 1)? '#3ecfbe' : '#FFF'"
        @click="options(h,d)"
        elevation="2"
        height="65px"
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
          color = primary
          @click="toggleActivity()"
        >
          <v-icon>mdi-plus</v-icon>Add Activity
        </v-btn>

        <v-btn 
          color = error
          @click="clearActivity()"
        >
          <v-icon>mdi-backspace-outline</v-icon>Clear Selected Cell
        </v-btn>
        
        <v-btn
          color = yellow
          @click="injectRandomMovie(sHour,sDay)"
        >
          <v-icon>mdi-movie-open-outline</v-icon>Reserve As Movie Time
        </v-btn>


        <v-btn
          color = green
          @click="injectRandom(sHour,sDay)"
        >
          <v-icon>mdi-alien-outline</v-icon>Random Activity
        </v-btn>

        <v-btn
          color= black class="white--text" @click="football = !football"
        >
          <v-icon>mdi-soccer</v-icon>Premier League Matches
        </v-btn>

        <v-btn color=purple @click="takeSS()">
            <v-icon>mdi-export</v-icon>Export Schedule
        </v-btn>
        <v-btn color=orange v-if="link !== ''" @click="forwardToImage()">
            <v-icon>mdi-eye</v-icon>Permalink
        </v-btn>

      </v-card-actions>

      <v-card  v-if="custom" class="rounded-0 ">
        <v-card-title>Add Activity</v-card-title>
        <v-text-field hint="Enter the activity." autofocus=true v-if="custom" v-model="activitySlot"></v-text-field>
        <v-card-actions>
          <v-btn color=primary @click="submitCustom()">
            <v-icon>mdi-content-save</v-icon> Save Activity
          </v-btn>
          <v-btn color=error @click="abortCustom()">
            <v-icon>mdi-close</v-icon> Cancel
          </v-btn>
        </v-card-actions>
      </v-card>

      <v-card  v-if="football" class="rounded-0 ">
        <v-card-title>Add Premier League Matches to Schedule</v-card-title>
        <v-card-actions>
          <v-dialog
            ref="dialog"
            v-model="modal"
            :return-value.sync="date"
            persistent
            width="290px"
          >
        <template v-slot:activator="{ on, attrs }">
          <v-text-field
            v-model="date"
            label="Pick Date"
            prepend-icon="mdi-calendar"
            readonly
            v-bind="attrs"
            v-on="on"
          ></v-text-field>
        </template>
        <v-date-picker
          v-model="date"
          scrollable
        >
          <v-spacer></v-spacer>
          <v-btn
            text
            color="primary"
            @click="modal = false"
          >
            Cancel
          </v-btn>
          <v-btn
            text
            color="primary"
            @click="matchesOnDate()"
          >
            OK
          </v-btn>
        </v-date-picker>
      </v-dialog>
        </v-card-actions>

      <span v-if="md.length !== 0" >
        <span
          v-for="i in md.length" :key=i
        >
          <v-btn color=#3ecfbe @click="selectMatch(i-1)">{{md[i-1][0]}} {{md[i-1][1]}}</v-btn>
          <br>
        </span>
      </span>
      <span v-else>
          No Matches on Selected Date
          
        </span>

      </v-card>

      
      
    </v-card>
  </v-container>
  </v-app>
</template>

<script>
  import axios from "axios"
  import html2canvas from "html2canvas"

  export default {
    name: 'WeeklySchedule',
    created() {
                setInterval(this.timeNow, 1000);
            },
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
      timestamp:"",
      link:"",
      football:false,
      picker:"",
      date:"",
      modal:"",
      matches:{},
      md:[],
    }),
    methods:{
      timeNow: function () {
          const today = new Date();
          const date = today.getDate()+'-'+(today.getMonth()+1)+'-'+today.getFullYear();
          //const time = today.getHours() + ":" + today.getMinutes() + ":" + today.getSeconds();
          const days = ["","Monday","Tuesday","Wednesday","Thursday","Friday","Saturday","Sunday"];
          const dateTime = "Today is " + date+ " " + days[today.getDay()];
          this.timestamp = dateTime;
      },
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
        // console.log(day,hour)
      },
      toggleActivity: function(){
        this.custom = !this.custom;
        this.football = false;
        // console.log("hour =", hour, "day = ", day)
      },
      clearActivity: function(){
        this.activities[this.sDay-1][this.sHour-1] = "";
      },
      injectRandom: function(h,d){
        axios.get('https://www.boredapi.com/api/activity/').then((response) => {
            this.activities[d-1][h-1] = response.data.activity;
        });
      },
      injectRandomMovie: function(h,d){
        axios.get('https://k2maan-moviehut.herokuapp.com/api/random').then((response) => {
            this.activities[d-1][h-1] = "Watch " + response.data.name;
        })
      },
      matchesOnDate: function(){
        this.$refs.dialog.save(this.date);
        this.matches = {};
        this.md = [];
        axios({
          url:'https://api.football-data.org/v2/competitions/2021/matches?dateFrom='+this.date+'&dateTo='+this.date,
          method:'GET',
          headers:{
            'X-Auth-Token': 'e91ccd273e854e68bdc11f33b698d3e7'
          }
        }).then((response) => {
          if(response.data['count'] === 0){
            // console.log("No matches today")
          }
          else{
            this.matches = response.data['matches'];
            // console.log(this.matches)
            // console.log(this.matches.length);
            for(var i = 0; i < this.matches.length; i++){
              var matchName = this.matches[i]['homeTeam']['name'] + " vs " + this.matches[i]['awayTeam']['name'];
              var matchTime = this.matches[i]['utcDate']
              var time = new Date(matchTime);
              //var xa = time.toLocaleTimeString();
              this.md.push([matchName, time]);
              // // console.log(this.md[i])
            }
           //// console.log(this.matches)
          }
            
        })
      },
      selectMatch: function(i){
        var time = new Date(this.md[i][1])
        var hour = time.getHours() - 7;
        var day = time.getDay();
        if(day == 0){
          day = 7;
        }
        // console.log(hour);
        // console.log(day)
        this.activities[day][hour] = this.md[i][0];
        // console.log(this.md[i][0])
      },
      submitCustom: function(){
        this.activities[this.sDay-1][this.sHour-1] = this.activitySlot;
        this.activitySlot = "";
        this.custom = false;
        return;
      },
      abortCustom: function(){
        this.activitySlot="";
        this.custom = false;
        return;
      },
      takeSS: function(){
        html2canvas(document.getElementById('SS')).then((canvas) => {
            var dataURL = canvas.toDataURL().replace(/.*,/, '');
            var form = new FormData();
            form.append("image", dataURL)
            axios.post("https://api.imgbb.com/1/upload?key=ab1c6965aa236df1602f21265fab19c6",form,{}).then((response) => {
                this.link = response.data.data.url
                // console.log(response.data.data)
            })
        });
      },
      forwardToImage: function(){
        window.open(this.link);
      }
    },
  }
</script>

<style scoped>

</style>