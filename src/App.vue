<template>
<div id="app">
  <v-app id="inspire">
    <v-navigation-drawer
      permanent
      :clipped="$vuetify.breakpoint.mdAndUp"
      app
      v-model="drawer"
    >

   



      <v-checkbox v-model="selected" label="Sports" value="Sports"></v-checkbox>
      <v-checkbox v-model="selected" label="Personal" value="Personal"></v-checkbox>
       <v-checkbox v-model="selected" label="Work" value="Work"></v-checkbox>

     <ul id="example-1">
      <li v-for="item in filteredEvents[selected[0]]">

      <v-card class="mx-auto" 
                :color="item.color" dark max-width="400">
    

          <v-expansion-panel>
      <v-expansion-panel-content
      >
        <template v-slot:header>
          <div>{{ item.title }} </div>
        </template>
        <v-card>
          <v-card-text>{{ item.details }} </v-card-text>
        </v-card>
      </v-expansion-panel-content>
    </v-expansion-panel>
  
      <v-card-actions>
        <v-list-tile class="grow">
  
          <v-list-tile-content>
            <v-list-tile-title>{{ item.category }} </v-list-tile-title>
          </v-list-tile-content>
  
          <v-layout
            align-center
            justify-end
          >
            <span class="subheading mr-2">{{ item.date }} </span>
          </v-layout>
        </v-list-tile>
      </v-card-actions>
    </v-card>

       
      </li>
    </ul>



  



    </v-navigation-drawer>

  <v-toolbar
      color="purple darken-3"
      dark
      app
      :clipped-left="$vuetify.breakpoint.mdAndUp"
      fixed
    >

      <p id="audio-control" class="white-circle">
        <img src="https://www.shareicon.net/data/48x48/2015/10/07/113732_audio_512x512.png">
        <canvas class="visualizer"></canvas>
    </p>
    <p><span id="message"></span></p>
    <p>
        <input type="text" id="BOT" name="BOT" placeholder="BOT" value="WeatherBot"/>
    </p>


      <v-toolbar-title style="width: 300px" class="ml-0 pl-3">
        <span class="hidden-sm-and-down"></span>
      </v-toolbar-title>


      <v-text-field
        flat
        solo-inverted
        prepend-icon="search"
        label="Search"
        class="hidden-sm-and-down"
      ></v-text-field>
      <v-spacer></v-spacer>
      <v-spacer></v-spacer>

      
    </v-toolbar>


<v-content>
      <v-dialog v-model="dialog" fullscreen hide-overlay transition="dialog-bottom-transition">
        <template v-slot:activator="{ on }">
          <v-btn color="primary" dark v-on="on">Add Event</v-btn>
        </template>
        <v-card>
          <v-toolbar dark color="primary">
            <v-btn icon dark @click="dialog = false">
              <v-icon>close</v-icon>
            </v-btn>
            <v-toolbar-title>Add Event</v-toolbar-title>
            <v-spacer></v-spacer>
            <v-toolbar-items>
              <v-btn dark flat @click="dialog = false">Save</v-btn>
            </v-toolbar-items>
          </v-toolbar>


          <v-container grid-list-md>
              <v-layout wrap>
                <v-flex xs12>
                  <v-text-field label="Event Name" v-model="titleBox" id="titleBox" required></v-text-field>
                </v-flex>
              
                </v-flex>
                <v-flex xs12>
                  <v-text-field label="Description" v-model="descriptionBox" id="descriptionBox" required></v-text-field>
                </v-flex>
                <v-flex xs12 sm6>
                  <v-select
                    :items="['Sports', 'Work', 'Personal']"
                    label="Category"
                    required
                    v-model="categoryBox" id="categoryBox"
                  ></v-select>
                </v-flex>


                 <v-menu
          ref="startMenu"
          v-model="startMenu"
          :close-on-content-click="false"
          :nudge-right="40"
          :return-value.sync="start"
          transition="scale-transition"
          min-width="290px"
          lazy
          offset-y
          full-width
           id="dateBox"
        >
          <template v-slot:activator="{ on }">
            <v-text-field
              v-model="start"
              label="Start Date"
              prepend-icon="event"
              readonly
              v-on="on"
            ></v-text-field>
          </template>
          <v-date-picker
            v-model="start"
            no-title
            scrollable
          >
            <v-spacer></v-spacer>
            <v-btn
              flat
              color="primary"
              @click="startMenu = false"
            >
              Cancel
            </v-btn>
            <v-btn
              flat
              color="primary"
              @click="$refs.startMenu.save(start)"
            >
              OK
            </v-btn>
          </v-date-picker>
        </v-menu>
        <v-menu
          v-if="hasEnd"
          ref="endMenu"
          v-model="endMenu"
          :close-on-content-click="false"
          :nudge-right="40"
          :return-value.sync="end"
          transition="scale-transition"
          min-width="290px"
          lazy
          offset-y
          full-width
        >
          <template v-slot:activator="{ on }">
            <v-text-field
              v-model="end"
              label="End Date"
              prepend-icon="event"
              readonly
              v-on="on"
            ></v-text-field>
          </template>
          <v-date-picker
            v-model="end"
            no-title
            scrollable
          >
            <v-spacer></v-spacer>
            <v-btn
              flat
              color="primary"
              @click="endMenu = false"
            >
              Cancel
            </v-btn>
            <v-btn
              flat
              color="primary"
              @click="$refs.endMenu.save(end)"
            >
              OK
            </v-btn>
          </v-date-picker>
        </v-menu>


              </v-layout>
              <v-btn depressed large color="rgba(255, 0, 0, 0.5)" v-on:click="greet">Add Event</v-btn>
            </v-container>
        </v-card>
      </v-dialog>
    <v-calendar
            :now="today"
            :value="today"
            color="primary"
            ref="calendar"
            v-model="start"
            :type="type"
            :start="start"
            :end="end"
            :min-weeks="minWeeks"
            :max-days="maxDays"
            :color="color"
          >
            <template v-slot:day="{ date }">
              <template v-for="event in eventsMap[date]">
                <v-menu
                  :key="event.title"
                  v-model="event.open"
                  full-width
                  offset-x

                >
                  <template :color="event.color" v-slot:activator="{ on }">
                    <div
                      v-if="!event.time"
                      v-ripple
                      class="my-event"
                      v-on="on"
                      v-html="event.title"
                      v-bind:style="{ backgroundColor: event.color }"
                    ></div>
                  </template>

                  <v-card
                    color="grey lighten-4"
                    min-width="350px"
                    flat
                  >
                    <v-toolbar
                      color="primary"
                      :color="event.color"
                      dark
                    >
                      <v-btn icon>
                        <v-icon>edit</v-icon>
                      </v-btn>
                      <v-toolbar-title v-html="event.title"></v-toolbar-title>
                      <v-spacer></v-spacer>
                      <v-btn icon>
                        <v-icon>favorite</v-icon>
                      </v-btn>
                      <v-btn icon>
                        <v-icon>more_vert</v-icon>
                      </v-btn>
                    </v-toolbar>
                    <v-card-title primary-title>
                      <span v-html="event.details"></span>
                    </v-card-title>
                    <v-card-actions>
                      <v-btn
                        flat
                        color="secondary"
                      >
                        Cancel
                      </v-btn>
                    </v-card-actions>
                  </v-card>
                </v-menu>
              </template>
            </template>
          </v-calendar>


          <WeekChart/>  

 

        
    </v-content>
    </v-app>
    </div>
</template>


<script>
  import WeekChart from "./components/WeekChart";
  export default {
    components: {WeekChart},
    data: () => ({
      dialog: false,
      notifications: false,
      sound: true,
      widgets: false,
      today: '2019-01-08',
      start: '2019-01-12',
      radioGroup: 1,
      filter: '2',
      selected: ['Work'],
      events: [
        {
          title: 'Vacation',
          category: 'Personal',
          color: '#26c6da',
          details: 'Going to the beach!',
          date: '2018-12-30',
          open: false
        },
        {
          title: 'Vacation',
          category: 'Personal',
          color: '#26c6da',
          details: 'Going to the beach!',
          date: '2018-12-31',
          open: false
        },
        {
          title: 'Vacation',
          category: 'Personal',
          color: '#26c6da',
          details: 'Going to the beach!',
          date: '2019-01-01',
          open: false
        },
        {
          title: 'Meeting',
          category: 'Work',
          color: '#FF4377',
          details: 'Spending time on how we do not have enough time',
          date: '2019-01-07',
          open: false
        },
        {
          title: '30th Birthday',
          category: 'Work',
          color: '#FF7043',
          details: 'Celebrate responsibly',
          date: '2019-01-03',
          open: false
        },
        {
          title: 'New Year',
          category: 'Work',
          color: '#FF4377',
          details: 'Eat chocolate until you pass out',
          date: '2019-01-01',
          open: false
        },
        {
          title: 'Conference',
          category: 'Work',
          color: '#FF4377',
          details: 'Mute myself the whole time and wonder why I am on this call',
          date: '2019-01-21',
          open: false
        },
        {
          title: 'Hackathon',
          category: 'Personal',
          color: '#FF7043',
          details: 'Code like there is no tommorrow',
          date: '2019-02-01',
          open: false
        },
        {
          title: 'Hornets vs Sixers',
          category: 'Sports',
          color: '#FF7043',
          details: 'Code like there is no tommorrow',
          date: '2019-02-01',
          open: false
        },



        {"category": "Sports", "title": "New Orleans Pelicans Vs. Dallas Mavericks", "color": "#FF7043", "details": "The New Orleans Pelicans play Dallas Mavericks at home", "date": "2018-12-28", "open": "false"}, {"category": "Sports", "title": "Denver Nuggets Vs. San Antonio Spurs", "color": "#FF7043", "details": "The Denver Nuggets play San Antonio Spurs at home", "date": "2018-12-28", "open": "false"}, {"category": "Sports", "title": "Phoenix Suns Vs. Oklahoma City Thunder", "color": "#FF7043", "details": "The Phoenix Suns play Oklahoma City Thunder at home", "date": "2018-12-28", "open": "false"}, {"category": "Sports", "title": "Los Angeles Lakers Vs. Los Angeles Clippers", "color": "#FF7043", "details": "The Los Angeles Lakers play Los Angeles Clippers at home", "date": "2018-12-28", "open": "false"}, {"category": "Sports", "title": "Milwaukee Bucks Vs. Brooklyn Nets", "color": "#FF7043", "details": "The Milwaukee Bucks play Brooklyn Nets at home", "date": "2018-12-29", "open": "false"}, {"category": "Sports", "title": "New Orleans Pelicans Vs. Houston Rockets", "color": "#FF7043", "details": "The New Orleans Pelicans play Houston Rockets at home", "date": "2018-12-29", "open": "false"}, {"category": "Sports", "title": "Washington Wizards Vs. Charlotte Hornets", "color": "#FF7043", "details": "The Washington Wizards play Charlotte Hornets at home", "date": "2018-12-29", "open": "false"}, {"category": "Sports", "title": "Atlanta Hawks Vs. Cleveland Cavaliers", "color": "#FF7043", "details": "The Atlanta Hawks play Cleveland Cavaliers at home", "date": "2018-12-29", "open": "false"}, {"category": "Sports", "title": "Memphis Grizzlies Vs. Boston Celtics", "color": "#FF7043", "details": "The Memphis Grizzlies play Boston Celtics at home", "date": "2018-12-29", "open": "false"}, {"category": "Sports", "title": "Utah Jazz Vs. New York Knicks", "color": "#FF7043", "details": "The Utah Jazz play New York Knicks at home", "date": "2018-12-29", "open": "false"}, {"category": "Sports", "title": "Phoenix Suns Vs. Denver Nuggets", "color": "#FF7043", "details": "The Phoenix Suns play Denver Nuggets at home", "date": "2018-12-29", "open": "false"}, {"category": "Sports", "title": "Portland Trail Blazers Vs. Golden State Warriors", "color": "#FF7043", "details": "The Portland Trail Blazers play Golden State Warriors at home", "date": "2018-12-29", "open": "false"}, {"category": "Sports", "title": "Los Angeles Clippers Vs. San Antonio Spurs", "color": "#FF7043", "details": "The Los Angeles Clippers play San Antonio Spurs at home", "date": "2018-12-29", "open": "false"}, {"category": "Sports", "title": "Orlando Magic Vs. Detroit Pistons", "color": "#FF7043", "details": "The Orlando Magic play Detroit Pistons at home", "date": "2018-12-30", "open": "false"}, {"category": "Sports", "title": "Miami Heat Vs. Minnesota Timberwolves", "color": "#FF7043", "details": "The Miami Heat play Minnesota Timberwolves at home", "date": "2018-12-30", "open": "false"}, {"category": "Sports", "title": "Toronto Raptors Vs. Chicago Bulls", "color": "#FF7043", "details": "The Toronto Raptors play Chicago Bulls at home", "date": "2018-12-30", "open": "false"}, {"category": "Sports", "title": "Dallas Mavericks Vs. Oklahoma City Thunder", "color": "#FF7043", "details": "The Dallas Mavericks play Oklahoma City Thunder at home", "date": "2018-12-30", "open": "false"}, {"category": "Sports", "title": "Portland Trail Blazers Vs. Philadelphia 76ers", "color": "#FF7043", "details": "The Portland Trail Blazers play Philadelphia 76ers at home", "date": "2018-12-30", "open": "false"}, {"category": "Sports", "title": "Los Angeles Lakers Vs. Sacramento Kings", "color": "#FF7043", "details": "The Los Angeles Lakers play Sacramento Kings at home", "date": "2018-12-30", "open": "false"}, {"category": "Sports", "title": "Indiana Pacers Vs. Atlanta Hawks", "color": "#FF7043", "details": "The Indiana Pacers play Atlanta Hawks at home", "date": "2018-12-31", "open": "false"}, {"category": "Sports", "title": "Charlotte Hornets Vs. Orlando Magic", "color": "#FF7043", "details": "The Charlotte Hornets play Orlando Magic at home", "date": "2018-12-31", "open": "false"}, {"category": "Sports", "title": "Houston Rockets Vs. Memphis Grizzlies", "color": "#FF7043", "details": "The Houston Rockets play Memphis Grizzlies at home", "date": "2018-12-31", "open": "false"}, {"category": "Sports", "title": "San Antonio Spurs Vs. Boston Celtics", "color": "#FF7043", "details": "The San Antonio Spurs play Boston Celtics at home", "date": "2018-12-31", "open": "false"}, {"category": "Sports", "title": "New Orleans Pelicans Vs. Minnesota Timberwolves", "color": "#FF7043", "details": "The New Orleans Pelicans play Minnesota Timberwolves at home", "date": "2018-12-31", "open": "false"}, {"category": "Sports", "title": "Oklahoma City Thunder Vs. Dallas Mavericks", "color": "#FF7043", "details": "The Oklahoma City Thunder play Dallas Mavericks at home", "date": "2018-12-31", "open": "false"}, {"category": "Sports", "title": "Phoenix Suns Vs. Golden State Warriors", "color": "#FF7043", "details": "The Phoenix Suns play Golden State Warriors at home", "date": "2018-12-31", "open": "false"}, {"category": "Sports", "title": "Toronto Raptors Vs. Utah Jazz", "color": "#FF7043", "details": "The Toronto Raptors play Utah Jazz at home", "date": "2019-01-01", "open": "false"}, {"category": "Sports", "title": "Milwaukee Bucks Vs. Detroit Pistons", "color": "#FF7043", "details": "The Milwaukee Bucks play Detroit Pistons at home", "date": "2019-01-01", "open": "false"}, {"category": "Sports", "title": "Denver Nuggets Vs. New York Knicks", "color": "#FF7043", "details": "The Denver Nuggets play New York Knicks at home", "date": "2019-01-01", "open": "false"}, {"category": "Sports", "title": "Sacramento Kings Vs. Portland Trail Blazers", "color": "#FF7043", "details": "The Sacramento Kings play Portland Trail Blazers at home", "date": "2019-01-01", "open": "false"}, {"category": "Sports", "title": "Los Angeles Clippers Vs. Philadelphia 76ers", "color": "#FF7043", "details": "The Los Angeles Clippers play Philadelphia 76ers at home", "date": "2019-01-01", "open": "false"}, {"category": "Sports", "title": "Charlotte Hornets Vs. Dallas Mavericks", "color": "#FF7043", "details": "The Charlotte Hornets play Dallas Mavericks at home", "date": "2019-01-02", "open": "false"}, {"category": "Sports", "title": "Cleveland Cavaliers Vs. Miami Heat", "color": "#FF7043", "details": "The Cleveland Cavaliers play Miami Heat at home", "date": "2019-01-02", "open": "false"}, {"category": "Sports", "title": "Washington Wizards Vs. Atlanta Hawks", "color": "#FF7043", "details": "The Washington Wizards play Atlanta Hawks at home", "date": "2019-01-02", "open": "false"}, {"category": "Sports", "title": "Brooklyn Nets Vs. New Orleans Pelicans", "color": "#FF7043", "details": "The Brooklyn Nets play New Orleans Pelicans at home", "date": "2019-01-02", "open": "false"}, {"category": "Sports", "title": "Boston Celtics Vs. Minnesota Timberwolves", "color": "#FF7043", "details": "The Boston Celtics play Minnesota Timberwolves at home", "date": "2019-01-02", "open": "false"}, {"category": "Sports", "title": "Chicago Bulls Vs. Orlando Magic", "color": "#FF7043", "details": "The Chicago Bulls play Orlando Magic at home", "date": "2019-01-02", "open": "false"}, {"category": "Sports", "title": "Memphis Grizzlies Vs. Detroit Pistons", "color": "#FF7043", "details": "The Memphis Grizzlies play Detroit Pistons at home", "date": "2019-01-02", "open": "false"}, {"category": "Sports", "title": "Phoenix Suns Vs. Philadelphia 76ers", "color": "#FF7043", "details": "The Phoenix Suns play Philadelphia 76ers at home", "date": "2019-01-02", "open": "false"}, {"category": "Sports", "title": "Los Angeles Lakers Vs. Oklahoma City Thunder", "color": "#FF7043", "details": "The Los Angeles Lakers play Oklahoma City Thunder at home", "date": "2019-01-02", "open": "false"}, {"category": "Sports", "title": "San Antonio Spurs Vs. Toronto Raptors", "color": "#FF7043", "details": "The San Antonio Spurs play Toronto Raptors at home", "date": "2019-01-03", "open": "false"}, {"category": "Sports", "title": "Sacramento Kings Vs. Denver Nuggets", "color": "#FF7043", "details": "The Sacramento Kings play Denver Nuggets at home", "date": "2019-01-03", "open": "false"}, {"category": "Sports", "title": "Golden State Warriors Vs. Houston Rockets", "color": "#FF7043", "details": "The Golden State Warriors play Houston Rockets at home", "date": "2019-01-03", "open": "false"}, {"category": "Sports", "title": "Cleveland Cavaliers Vs. Utah Jazz", "color": "#FF7043", "details": "The Cleveland Cavaliers play Utah Jazz at home", "date": "2019-01-04", "open": "false"}, {"category": "Sports", "title": "Boston Celtics Vs. Dallas Mavericks", "color": "#FF7043", "details": "The Boston Celtics play Dallas Mavericks at home", "date": "2019-01-04", "open": "false"}, {"category": "Sports", "title": "Miami Heat Vs. Washington Wizards", "color": "#FF7043", "details": "The Miami Heat play Washington Wizards at home", "date": "2019-01-04", "open": "false"}, {"category": "Sports", "title": "Chicago Bulls Vs. Indiana Pacers", "color": "#FF7043", "details": "The Chicago Bulls play Indiana Pacers at home", "date": "2019-01-04", "open": "false"}, {"category": "Sports", "title": "Memphis Grizzlies Vs. Brooklyn Nets", "color": "#FF7043", "details": "The Memphis Grizzlies play Brooklyn Nets at home", "date": "2019-01-04", "open": "false"}, {"category": "Sports", "title": "Minnesota Timberwolves Vs. Orlando Magic", "color": "#FF7043", "details": "The Minnesota Timberwolves play Orlando Magic at home", "date": "2019-01-04", "open": "false"}, {"category": "Sports", "title": "Milwaukee Bucks Vs. Atlanta Hawks", "color": "#FF7043", "details": "The Milwaukee Bucks play Atlanta Hawks at home", "date": "2019-01-04", "open": "false"}, {"category": "Sports", "title": "Phoenix Suns Vs. Los Angeles Clippers", "color": "#FF7043", "details": "The Phoenix Suns play Los Angeles Clippers at home", "date": "2019-01-04", "open": "false"}, {"category": "Sports", "title": "Los Angeles Lakers Vs. New York Knicks", "color": "#FF7043", "details": "The Los Angeles Lakers play New York Knicks at home", "date": "2019-01-04", "open": "false"}, {"category": "Sports", "title": "Portland Trail Blazers Vs. Oklahoma City Thunder", "color": "#FF7043", "details": "The Portland Trail Blazers play Oklahoma City Thunder at home", "date": "2019-01-04", "open": "false"}, {"category": "Sports", "title": "Denver Nuggets Vs. Charlotte Hornets", "color": "#FF7043", "details": "The Denver Nuggets play Charlotte Hornets at home", "date": "2019-01-05", "open": "false"}, {"category": "Sports", "title": "Detroit Pistons Vs. Utah Jazz", "color": "#FF7043", "details": "The Detroit Pistons play Utah Jazz at home", "date": "2019-01-05", "open": "false"}, {"category": "Sports", "title": "Philadelphia 76ers Vs. Dallas Mavericks", "color": "#FF7043", "details": "The Philadelphia 76ers play Dallas Mavericks at home", "date": "2019-01-05", "open": "false"}, {"category": "Sports", "title": "Cleveland Cavaliers Vs. New Orleans Pelicans", "color": "#FF7043", "details": "The Cleveland Cavaliers play New Orleans Pelicans at home", "date": "2019-01-05", "open": "false"}, {"category": "Sports", "title": "Milwaukee Bucks Vs. Toronto Raptors", "color": "#FF7043", "details": "The Milwaukee Bucks play Toronto Raptors at home", "date": "2019-01-05", "open": "false"}, {"category": "Sports", "title": "San Antonio Spurs Vs. Memphis Grizzlies", "color": "#FF7043", "details": "The San Antonio Spurs play Memphis Grizzlies at home", "date": "2019-01-05", "open": "false"}, {"category": "Sports", "title": "Portland Trail Blazers Vs. Houston Rockets", "color": "#FF7043", "details": "The Portland Trail Blazers play Houston Rockets at home", "date": "2019-01-05", "open": "false"}, {"category": "Sports", "title": "Sacramento Kings Vs. Golden State Warriors", "color": "#FF7043", "details": "The Sacramento Kings play Golden State Warriors at home", "date": "2019-01-05", "open": "false"}, {"category": "Sports", "title": "Chicago Bulls Vs. Brooklyn Nets", "color": "#FF7043", "details": "The Chicago Bulls play Brooklyn Nets at home", "date": "2019-01-06", "open": "false"}, {"category": "Sports", "title": "Minnesota Timberwolves Vs. Los Angeles Lakers", "color": "#FF7043", "details": "The Minnesota Timberwolves play Los Angeles Lakers at home", "date": "2019-01-06", "open": "false"}, {"category": "Sports", "title": "Los Angeles Clippers Vs. Orlando Magic", "color": "#FF7043", "details": "The Los Angeles Clippers play Orlando Magic at home", "date": "2019-01-06", "open": "false"}, {"category": "Sports", "title": "Atlanta Hawks Vs. Miami Heat", "color": "#FF7043", "details": "The Atlanta Hawks play Miami Heat at home", "date": "2019-01-06", "open": "false"}, {"category": "Sports", "title": "Oklahoma City Thunder Vs. Washington Wizards", "color": "#FF7043", "details": "The Oklahoma City Thunder play Washington Wizards at home", "date": "2019-01-06", "open": "false"}, {"category": "Sports", "title": "Toronto Raptors Vs. Indiana Pacers", "color": "#FF7043", "details": "The Toronto Raptors play Indiana Pacers at home", "date": "2019-01-06", "open": "false"}, {"category": "Sports", "title": "Phoenix Suns Vs. Charlotte Hornets", "color": "#FF7043", "details": "The Phoenix Suns play Charlotte Hornets at home", "date": "2019-01-06", "open": "false"},
        {"category": "Sports", "title": "Detroit Pistons Vs. San Antonio Spurs", "color": "#FF7043", "details": "The Detroit Pistons play San Antonio Spurs at home", "date": "2019-01-07", "open": "false"}, {"category": "Sports", "title": "Boston Celtics Vs. Brooklyn Nets", "color": "#FF7043", "details": "The Boston Celtics play Brooklyn Nets at home", "date": "2019-01-07", "open": "false"}, {"category": "Sports", "title": "Houston Rockets Vs. Denver Nuggets", "color": "#FF7043", "details": "The Houston Rockets play Denver Nuggets at home", "date": "2019-01-07", "open": "false"}, {"category": "Sports", "title": "Milwaukee Bucks Vs. Utah Jazz", "color": "#FF7043", "details": "The Milwaukee Bucks play Utah Jazz at home", "date": "2019-01-07", "open": "false"}, {"category": "Sports", "title": "New Orleans Pelicans Vs. Memphis Grizzlies", "color": "#FF7043", "details": "The New Orleans Pelicans play Memphis Grizzlies at home", "date": "2019-01-07", "open": "false"}, {"category": "Sports", "title": "Dallas Mavericks Vs. Los Angeles Lakers", "color": "#FF7043", "details": "The Dallas Mavericks play Los Angeles Lakers at home", "date": "2019-01-07", "open": "false"}, {"category": "Sports", "title": "Portland Trail Blazers Vs. New York Knicks", "color": "#FF7043", "details": "The Portland Trail Blazers play New York Knicks at home", "date": "2019-01-07", "open": "false"}, {"category": "Sports", "title": "Sacramento Kings Vs. Orlando Magic", "color": "#FF7043", "details": "The Sacramento Kings play Orlando Magic at home", "date": "2019-01-07", "open": "false"}, {"category": "Sports", "title": "Cleveland Cavaliers Vs. Indiana Pacers", "color": "#FF7043", "details": "The Cleveland Cavaliers play Indiana Pacers at home", "date": "2019-01-08", "open": "false"}, {"category": "Sports", "title": "Philadelphia 76ers Vs. Washington Wizards", "color": "#FF7043", "details": "The Philadelphia 76ers play Washington Wizards at home", "date": "2019-01-08", "open": "false"}, {"category": "Sports", "title": "Miami Heat Vs. Denver Nuggets", "color": "#FF7043", "details": "The Miami Heat play Denver Nuggets at home", "date": "2019-01-08", "open": "false"}, {"category": "Sports", "title": "Toronto Raptors Vs. Atlanta Hawks", "color": "#FF7043", "details": "The Toronto Raptors play Atlanta Hawks at home", "date": "2019-01-08", "open": "false"}, {"category": "Sports", "title": "Oklahoma City Thunder Vs. Minnesota Timberwolves", "color": "#FF7043", "details": "The Oklahoma City Thunder play Minnesota Timberwolves at home", "date": "2019-01-08", "open": "false"}, {"category": "Sports", "title": "Phoenix Suns Vs. Sacramento Kings", "color": "#FF7043", "details": "The Phoenix Suns play Sacramento Kings at home", "date": "2019-01-08", "open": "false"}, {"category": "Sports", "title": "Golden State Warriors Vs. New York Knicks", "color": "#FF7043", "details": "The Golden State Warriors play New York Knicks at home", "date": "2019-01-08", "open": "false"}, {"category": "Sports", "title": "Los Angeles Clippers Vs. Charlotte Hornets", "color": "#FF7043", "details": "The Los Angeles Clippers play Charlotte Hornets at home", "date": "2019-01-08", "open": "false"}, {"category": "Sports", "title": "Boston Celtics Vs. Indiana Pacers", "color": "#FF7043", "details": "The Boston Celtics play Indiana Pacers at home", "date": "2019-01-09", "open": "false"}, {"category": "Sports", "title": "Washington Wizards Vs. Philadelphia 76ers", "color": "#FF7043", "details": "The Washington Wizards play Philadelphia 76ers at home", "date": "2019-01-09", "open": "false"}, {"category": "Sports", "title": "Brooklyn Nets Vs. Atlanta Hawks", "color": "#FF7043", "details": "The Brooklyn Nets play Atlanta Hawks at home", "date": "2019-01-09", "open": "false"}, {"category": "Sports", "title": "Houston Rockets Vs. Milwaukee Bucks", "color": "#FF7043", "details": "The Houston Rockets play Milwaukee Bucks at home", "date": "2019-01-09", "open": "false"}, {"category": "Sports", "title": "Memphis Grizzlies Vs. San Antonio Spurs", "color": "#FF7043", "details": "The Memphis Grizzlies play San Antonio Spurs at home", "date": "2019-01-09", "open": "false"}, {"category": "Sports", "title": "New Orleans Pelicans Vs. Cleveland Cavaliers", "color": "#FF7043", "details": "The New Orleans Pelicans play Cleveland Cavaliers at home", "date": "2019-01-09", "open": "false"}, {"category": "Sports", "title": "Dallas Mavericks Vs. Phoenix Suns", "color": "#FF7043", "details": "The Dallas Mavericks play Phoenix Suns at home", "date": "2019-01-09", "open": "false"}, {"category": "Sports", "title": "Utah Jazz Vs. Orlando Magic", "color": "#FF7043", "details": "The Utah Jazz play Orlando Magic at home", "date": "2019-01-09", "open": "false"}, {"category": "Sports", "title": "Portland Trail Blazers Vs. Chicago Bulls", "color": "#FF7043", "details": "The Portland Trail Blazers play Chicago Bulls at home", "date": "2019-01-09", "open": "false"}, {"category": "Sports", "title": "Los Angeles Lakers Vs. Detroit Pistons", "color": "#FF7043", "details": "The Los Angeles Lakers play Detroit Pistons at home", "date": "2019-01-09", "open": "false"}, {"category": "Sports", "title": "Miami Heat Vs. Boston Celtics", "color": "#FF7043", "details": "The Miami Heat play Boston Celtics at home", "date": "2019-01-10", "open": "false"}, {"category": "Sports", "title": "Denver Nuggets Vs. Los Angeles Clippers", "color": "#FF7043", "details": "The Denver Nuggets play Los Angeles Clippers at home", "date": "2019-01-10", "open": "false"}, {"category": "Sports", "title": "San Antonio Spurs Vs. Oklahoma City Thunder", "color": "#FF7043", "details": "The San Antonio Spurs play Oklahoma City Thunder at home", "date": "2019-01-10", "open": "false"}, {"category": "Sports", "title": "Sacramento Kings Vs. Detroit Pistons", "color": "#FF7043", "details": "The Sacramento Kings play Detroit Pistons at home", "date": "2019-01-10", "open": "false"}, {"category": "Sports", "title": "Philadelphia 76ers Vs. Atlanta Hawks", "color": "#FF7043", "details": "The Philadelphia 76ers play Atlanta Hawks at home", "date": "2019-01-11", "open": "false"}, {"category": "Sports", "title": "Washington Wizards Vs. Milwaukee Bucks", "color": "#FF7043", "details": "The Washington Wizards play Milwaukee Bucks at home", "date": "2019-01-11", "open": "false"}, {"category": "Sports", "title": "New York Knicks Vs. Indiana Pacers", "color": "#FF7043", "details": "The New York Knicks play Indiana Pacers at home", "date": "2019-01-11", "open": "false"}, {"category": "Sports", "title": "Toronto Raptors Vs. Brooklyn Nets", "color": "#FF7043", "details": "The Toronto Raptors play Brooklyn Nets at home", "date": "2019-01-11", "open": "false"}, {"category": "Sports", "title": "Houston Rockets Vs. Cleveland Cavaliers", "color": "#FF7043", "details": "The Houston Rockets play Cleveland Cavaliers at home", "date": "2019-01-11", "open": "false"}, {"category": "Sports", "title": "Minnesota Timberwolves Vs. Dallas Mavericks", "color": "#FF7043", "details": "The Minnesota Timberwolves play Dallas Mavericks at home", "date": "2019-01-11", "open": "false"}, {"category": "Sports", "title": "Utah Jazz Vs. Los Angeles Lakers", "color": "#FF7043", "details": "The Utah Jazz play Los Angeles Lakers at home", "date": "2019-01-11", "open": "false"}, {"category": "Sports", "title": "Portland Trail Blazers Vs. Charlotte Hornets", "color": "#FF7043", "details": "The Portland Trail Blazers play Charlotte Hornets at home", "date": "2019-01-11", "open": "false"}, {"category": "Sports", "title": "Golden State Warriors Vs. Chicago Bulls", "color": "#FF7043", "details": "The Golden State Warriors play Chicago Bulls at home", "date": "2019-01-11", "open": "false"}, {"category": "Sports", "title": "Los Angeles Clippers Vs. Detroit Pistons", "color": "#FF7043", "details": "The Los Angeles Clippers play Detroit Pistons at home", "date": "2019-01-12", "open": "false"}, {"category": "Sports", "title": "Miami Heat Vs. Memphis Grizzlies", "color": "#FF7043", "details": "The Miami Heat play Memphis Grizzlies at home", "date": "2019-01-12", "open": "false"}, {"category": "Sports", "title": "Orlando Magic Vs. Boston Celtics", "color": "#FF7043", "details": "The Orlando Magic play Boston Celtics at home", "date": "2019-01-12", "open": "false"}, {"category": "Sports", "title": "Minnesota Timberwolves Vs. New Orleans Pelicans", "color": "#FF7043", "details": "The Minnesota Timberwolves play New Orleans Pelicans at home", "date": "2019-01-12", "open": "false"}, {"category": "Sports", "title": "Oklahoma City Thunder Vs. San Antonio Spurs", "color": "#FF7043", "details": "The Oklahoma City Thunder play San Antonio Spurs at home", "date": "2019-01-12", "open": "false"}, {"category": "Sports", "title": "Phoenix Suns Vs. Denver Nuggets", "color": "#FF7043", "details": "The Phoenix Suns play Denver Nuggets at home", "date": "2019-01-12", "open": "false"}, {"category": "Sports", "title": "Sacramento Kings Vs. Charlotte Hornets", "color": "#FF7043", "details": "The Sacramento Kings play Charlotte Hornets at home", "date": "2019-01-12", "open": "false"}, {"category": "Sports", "title": "Utah Jazz Vs. Chicago Bulls", "color": "#FF7043", "details": "The Utah Jazz play Chicago Bulls at home", "date": "2019-01-12", "open": "false"}, {"category": "Sports", "title": "New York Knicks Vs. Philadelphia 76ers", "color": "#FF7043", "details": "The New York Knicks play Philadelphia 76ers at home", "date": "2019-01-13", "open": "false"}, {"category": "Sports", "title": "Washington Wizards Vs. Toronto Raptors", "color": "#FF7043", "details": "The Washington Wizards play Toronto Raptors at home", "date": "2019-01-13", "open": "false"}, {"category": "Sports", "title": "Atlanta Hawks Vs. Milwaukee Bucks", "color": "#FF7043", "details": "The Atlanta Hawks play Milwaukee Bucks at home", "date": "2019-01-13", "open": "false"}, {"category": "Sports", "title": "Orlando Magic Vs. Houston Rockets", "color": "#FF7043", "details": "The Orlando Magic play Houston Rockets at home", "date": "2019-01-13", "open": "false"}, {"category": "Sports", "title": "Dallas Mavericks Vs. Golden State Warriors", "color": "#FF7043", "details": "The Dallas Mavericks play Golden State Warriors at home", "date": "2019-01-13", "open": "false"}, {"category": "Sports", "title": "Denver Nuggets Vs. Portland Trail Blazers", "color": "#FF7043", "details": "The Denver Nuggets play Portland Trail Blazers at home", "date": "2019-01-13", "open": "false"}, {"category": "Sports", "title": "Los Angeles Lakers Vs. Cleveland Cavaliers", "color": "#FF7043", "details": "The Los Angeles Lakers play Cleveland Cavaliers at home", "date": "2019-01-13", "open": "false"}, {"category": "Sports", "title": "Brooklyn Nets Vs. Boston Celtics", "color": "#FF7043", "details": "The Brooklyn Nets play Boston Celtics at home", "date": "2019-01-14", "open": "false"}, {"category": "Sports", "title": "Houston Rockets Vs. Memphis Grizzlies", "color": "#FF7043", "details": "The Houston Rockets play Memphis Grizzlies at home", "date": "2019-01-14", "open": "false"}, {"category": "Sports", "title": "San Antonio Spurs Vs. Charlotte Hornets", "color": "#FF7043", "details": "The San Antonio Spurs play Charlotte Hornets at home", "date": "2019-01-14", "open": "false"}, {"category": "Sports", "title": "Utah Jazz Vs. Detroit Pistons", "color": "#FF7043", "details": "The Utah Jazz play Detroit Pistons at home", "date": "2019-01-14", "open": "false"}, {"category": "Sports", "title": "Sacramento Kings Vs. Portland Trail Blazers", "color": "#FF7043", "details": "The Sacramento Kings play Portland Trail Blazers at home", "date": "2019-01-14", "open": "false"}, {"category": "Sports", "title": "Los Angeles Clippers Vs. New Orleans Pelicans", "color": "#FF7043", "details": "The Los Angeles Clippers play New Orleans Pelicans at home", "date": "2019-01-14", "open": "false"}, {"category": "Sports", "title": "Indiana Pacers Vs. Phoenix Suns", "color": "#FF7043", "details": "The Indiana Pacers play Phoenix Suns at home", "date": "2019-01-15", "open": "false"}, {"category": "Sports", "title": "Philadelphia 76ers Vs. Minnesota Timberwolves", "color": "#FF7043", "details": "The Philadelphia 76ers play Minnesota Timberwolves at home", "date": "2019-01-15", "open": "false"}, {"category": "Sports", "title": "Atlanta Hawks Vs. Oklahoma City Thunder", "color": "#FF7043", "details": "The Atlanta Hawks play Oklahoma City Thunder at home", "date": "2019-01-15", "open": "false"}, {"category": "Sports", "title": "Milwaukee Bucks Vs. Miami Heat", "color": "#FF7043", "details": "The Milwaukee Bucks play Miami Heat at home", "date": "2019-01-15", "open": "false"}, {"category": "Sports", "title": "Denver Nuggets Vs. Golden State Warriors", "color": "#FF7043", "details": "The Denver Nuggets play Golden State Warriors at home", "date": "2019-01-15", "open": "false"}, {"category": "Sports", "title": "Los Angeles Lakers Vs. Chicago Bulls", "color": "#FF7043", "details": "The Los Angeles Lakers play Chicago Bulls at home", "date": "2019-01-15", "open": "false"}, {"category": "Sports", "title": "Detroit Pistons Vs. Orlando Magic", "color": "#FF7043", "details": "The Detroit Pistons play Orlando Magic at home", "date": "2019-01-16", "open": "false"}, {"category": "Sports", "title": "Boston Celtics Vs. Toronto Raptors", "color": "#FF7043", "details": "The Boston Celtics play Toronto Raptors at home", "date": "2019-01-16", "open": "false"}, {"category": "Sports", "title": "Houston Rockets Vs. Brooklyn Nets", "color": "#FF7043", "details": "The Houston Rockets play Brooklyn Nets at home", "date": "2019-01-16", "open": "false"}, {"category": "Sports", "title": "Memphis Grizzlies Vs. Milwaukee Bucks", "color": "#FF7043", "details": "The Memphis Grizzlies play Milwaukee Bucks at home", "date": "2019-01-16", "open": "false"}, {"category": "Sports", "title": "Dallas Mavericks Vs. San Antonio Spurs", "color": "#FF7043", "details": "The Dallas Mavericks play San Antonio Spurs at home", "date": "2019-01-16", "open": "false"}, {"category": "Sports", "title": "Portland Trail Blazers Vs. Cleveland Cavaliers", "color": "#FF7043", "details": "The Portland Trail Blazers play Cleveland Cavaliers at home", "date": "2019-01-16", "open": "false"}, {"category": "Sports", "title": "Golden State Warriors Vs. New Orleans Pelicans", "color": "#FF7043", "details": "The Golden State Warriors play New Orleans Pelicans at home", "date": "2019-01-16", "open": "false"}, {"category": "Sports", "title": "Los Angeles Clippers Vs. Utah Jazz", "color": "#FF7043", "details": "The Los Angeles Clippers play Utah Jazz at home", "date": "2019-01-16", "open": "false"}, {"category": "Sports", "title": "Washington Wizards Vs. New York Knicks", "color": "#FF7043", "details": "The Washington Wizards play New York Knicks at home", "date": "2019-01-17", "open": "false"}, {"category": "Sports", "title": "Charlotte Hornets Vs. Sacramento Kings", "color": "#FF7043", "details": "The Charlotte Hornets play Sacramento Kings at home", "date": "2019-01-17", "open": "false"}, {"category": "Sports", "title": "Indiana Pacers Vs. Philadelphia 76ers", "color": "#FF7043", "details": "The Indiana Pacers play Philadelphia 76ers at home", "date": "2019-01-17", "open": "false"}, {"category": "Sports", "title": "Toronto Raptors Vs. Phoenix Suns", "color": "#FF7043", "details": "The Toronto Raptors play Phoenix Suns at home", "date": "2019-01-17", "open": "false"}, {"category": "Sports", "title": "Denver Nuggets Vs. Chicago Bulls", "color": "#FF7043", "details": "The Denver Nuggets play Chicago Bulls at home", "date": "2019-01-17", "open": "false"}, {"category": "Sports", "title": "Oklahoma City Thunder Vs. Los Angeles Lakers", "color": "#FF7043", "details": "The Oklahoma City Thunder play Los Angeles Lakers at home", "date": "2019-01-17", "open": "false"}, {"category": "Sports", "title": "Boston Celtics Vs. Memphis Grizzlies", "color": "#FF7043", "details": "The Boston Celtics play Memphis Grizzlies at home", "date": "2019-01-18", "open": "false"}, {"category": "Sports", "title": "Detroit Pistons Vs. Miami Heat", "color": "#FF7043", "details": "The Detroit Pistons play Miami Heat at home", "date": "2019-01-18", "open": "false"}, {"category": "Sports", "title": "Orlando Magic Vs. Brooklyn Nets", "color": "#FF7043", "details": "The Orlando Magic play Brooklyn Nets at home", "date": "2019-01-18", "open": "false"}, {"category": "Sports", "title": "Minnesota Timberwolves Vs. San Antonio Spurs", "color": "#FF7043", "details": "The Minnesota Timberwolves play San Antonio Spurs at home", "date": "2019-01-18", "open": "false"}, {"category": "Sports", "title": "Utah Jazz Vs. Cleveland Cavaliers", "color": "#FF7043", "details": "The Utah Jazz play Cleveland Cavaliers at home", "date": "2019-01-18", "open": "false"}, {"category": "Sports", "title": "Los Angeles Clippers Vs. Golden State Warriors", "color": "#FF7043", "details": "The Los Angeles Clippers play Golden State Warriors at home", "date": "2019-01-18", "open": "false"}, {"category": "Sports", "title": "Portland Trail Blazers Vs. New Orleans Pelicans", "color": "#FF7043", "details": "The Portland Trail Blazers play New Orleans Pelicans at home", "date": "2019-01-18", "open": "false"}, {"category": "Sports", "title": "Philadelphia 76ers Vs. Oklahoma City Thunder", "color": "#FF7043", "details": "The Philadelphia 76ers play Oklahoma City Thunder at home", "date": "2019-01-19", "open": "false"}, {"category": "Sports", "title": "Charlotte Hornets Vs. Phoenix Suns", "color": "#FF7043", "details": "The Charlotte Hornets play Phoenix Suns at home", "date": "2019-01-19", "open": "false"}, {"category": "Sports", "title": "Detroit Pistons Vs. Sacramento Kings", "color": "#FF7043", "details": "The Detroit Pistons play Sacramento Kings at home", "date": "2019-01-19", "open": "false"}, {"category": "Sports", "title": "Indiana Pacers Vs. Dallas Mavericks", "color": "#FF7043", "details": "The Indiana Pacers play Dallas Mavericks at home", "date": "2019-01-19", "open": "false"}, {"category": "Sports", "title": "Orlando Magic Vs. Milwaukee Bucks", "color": "#FF7043", "details": "The Orlando Magic play Milwaukee Bucks at home", "date": "2019-01-19", "open": "false"}, {"category": "Sports", "title": "Atlanta Hawks Vs. Boston Celtics", "color": "#FF7043", "details": "The Atlanta Hawks play Boston Celtics at home", "date": "2019-01-19", "open": "false"}, {"category": "Sports", "title": "Toronto Raptors Vs. Memphis Grizzlies", "color": "#FF7043", "details": "The Toronto Raptors play Memphis Grizzlies at home", "date": "2019-01-19", "open": "false"}, {"category": "Sports", "title": "Chicago Bulls Vs. Miami Heat", "color": "#FF7043", "details": "The Chicago Bulls play Miami Heat at home", "date": "2019-01-19", "open": "false"}, {"category": "Sports", "title": "Houston Rockets Vs. Los Angeles Lakers", "color": "#FF7043", "details": "The Houston Rockets play Los Angeles Lakers at home", "date": "2019-01-19", "open": "false"}, {"category": "Sports", "title": "Denver Nuggets Vs. Cleveland Cavaliers", "color": "#FF7043", "details": "The Denver Nuggets play Cleveland Cavaliers at home", "date": "2019-01-19", "open": "false"}, {"category": "Sports", "title": "Indiana Pacers Vs. Charlotte Hornets", "color": "#FF7043", "details": "The Indiana Pacers play Charlotte Hornets at home", "date": "2019-01-20", "open": "false"}, {"category": "Sports", "title": "Minnesota Timberwolves Vs. Phoenix Suns", "color": "#FF7043", "details": "The Minnesota Timberwolves play Phoenix Suns at home", "date": "2019-01-20", "open": "false"}, {"category": "Sports", "title": "San Antonio Spurs Vs. Los Angeles Clippers", "color": "#FF7043", "details": "The San Antonio Spurs play Los Angeles Clippers at home", "date": "2019-01-20", "open": "false"}, {"category": "Sports", "title": "New York Knicks Vs. Oklahoma City Thunder", "color": "#FF7043", "details": "The New York Knicks play Oklahoma City Thunder at home", "date": "2019-01-21", "open": "false"}, {"category": "Sports", "title": "Cleveland Cavaliers Vs. Chicago Bulls", "color": "#FF7043", "details": "The Cleveland Cavaliers play Chicago Bulls at home", "date": "2019-01-21", "open": "false"}, {"category": "Sports", "title": "Washington Wizards Vs. Detroit Pistons", "color": "#FF7043", "details": "The Washington Wizards play Detroit Pistons at home", "date": "2019-01-21", "open": "false"}, {"category": "Sports", "title": "Milwaukee Bucks Vs. Dallas Mavericks", "color": "#FF7043", "details": "The Milwaukee Bucks play Dallas Mavericks at home", "date": "2019-01-21", "open": "false"}, {"category": "Sports", "title": "Atlanta Hawks Vs. Orlando Magic", "color": "#FF7043", "details": "The Atlanta Hawks play Orlando Magic at home", "date": "2019-01-21", "open": "false"}, {"category": "Sports", "title": "Brooklyn Nets Vs. Sacramento Kings", "color": "#FF7043", "details": "The Brooklyn Nets play Sacramento Kings at home", "date": "2019-01-21", "open": "false"}, {"category": "Sports", "title": "Memphis Grizzlies Vs. New Orleans Pelicans", "color": "#FF7043", "details": "The Memphis Grizzlies play New Orleans Pelicans at home", "date": "2019-01-21", "open": "false"}, {"category": "Sports", "title": "Boston Celtics Vs. Miami Heat", "color": "#FF7043", "details": "The Boston Celtics play Miami Heat at home", "date": "2019-01-21", "open": "false"}, {"category": "Sports", "title": "Philadelphia 76ers Vs. Houston Rockets", "color": "#FF7043", "details": "The Philadelphia 76ers play Houston Rockets at home", "date": "2019-01-21", "open": "false"}, {"category": "Sports", "title": "Utah Jazz Vs. Portland Trail Blazers", "color": "#FF7043", "details": "The Utah Jazz play Portland Trail Blazers at home", "date": "2019-01-21", "open": "false"}, {"category": "Sports", "title": "Los Angeles Lakers Vs. Golden State Warriors", "color": "#FF7043", "details": "The Los Angeles Lakers play Golden State Warriors at home", "date": "2019-01-21", "open": "false"}, {"category": "Sports", "title": "Toronto Raptors Vs. Sacramento Kings", "color": "#FF7043", "details": "The Toronto Raptors play Sacramento Kings at home", "date": "2019-01-22", "open": "false"}, {"category": "Sports", "title": "Oklahoma City Thunder Vs. Portland Trail Blazers", "color": "#FF7043", "details": "The Oklahoma City Thunder play Portland Trail Blazers at home", "date": "2019-01-22", "open": "false"}, {"category": "Sports", "title": "Dallas Mavericks Vs. Los Angeles Clippers", "color": "#FF7043", "details": "The Dallas Mavericks play Los Angeles Clippers at home", "date": "2019-01-22", "open": "false"}, {"category": "Sports", "title": "Phoenix Suns Vs. Minnesota Timberwolves", "color": "#FF7043", "details": "The Phoenix Suns play Minnesota Timberwolves at home", "date": "2019-01-22", "open": "false"}, {"category": "Sports", "title": "Indiana Pacers Vs. Toronto Raptors", "color": "#FF7043", "details": "The Indiana Pacers play Toronto Raptors at home", "date": "2019-01-23", "open": "false"}, {"category": "Sports", "title": "Boston Celtics Vs. Cleveland Cavaliers", "color": "#FF7043", "details": "The Boston Celtics play Cleveland Cavaliers at home", "date": "2019-01-23", "open": "false"}, {"category": "Sports", "title": "Brooklyn Nets Vs. Orlando Magic", "color": "#FF7043", "details": "The Brooklyn Nets play Orlando Magic at home", "date": "2019-01-23", "open": "false"}, {"category": "Sports", "title": "Miami Heat Vs. Los Angeles Clippers", "color": "#FF7043", "details": "The Miami Heat play Los Angeles Clippers at home", "date": "2019-01-23", "open": "false"}, {"category": "Sports", "title": "New York Knicks Vs. Houston Rockets", "color": "#FF7043", "details": "The New York Knicks play Houston Rockets at home", "date": "2019-01-23", "open": "false"}, {"category": "Sports", "title": "Philadelphia 76ers Vs. San Antonio Spurs", "color": "#FF7043", "details": "The Philadelphia 76ers play San Antonio Spurs at home", "date": "2019-01-23", "open": "false"}, {"category": "Sports", "title": "Chicago Bulls Vs. Atlanta Hawks", "color": "#FF7043", "details": "The Chicago Bulls play Atlanta Hawks at home", "date": "2019-01-23", "open": "false"}, {"category": "Sports", "title": "Memphis Grizzlies Vs. Charlotte Hornets", "color": "#FF7043", "details": "The Memphis Grizzlies play Charlotte Hornets at home", "date": "2019-01-23", "open": "false"}, {"category": "Sports", "title": "New Orleans Pelicans Vs. Detroit Pistons", "color": "#FF7043", "details": "The New Orleans Pelicans play Detroit Pistons at home", "date": "2019-01-23", "open": "false"}, {"category": "Sports", "title": "Utah Jazz Vs. Denver Nuggets", "color": "#FF7043", "details": "The Utah Jazz play Denver Nuggets at home", "date": "2019-01-23", "open": "false"}, {"category": "Sports", "title": "Washington Wizards Vs. Golden State Warriors", "color": "#FF7043", "details": "The Washington Wizards play Golden State Warriors at home", "date": "2019-01-24", "open": "false"},
        {"category": "Sports", "title": "Oklahoma City Thunder Vs. New Orleans Pelicans", "color": "#FF7043", "details": "The Oklahoma City Thunder play New Orleans Pelicans at home", "date": "2019-01-24", "open": "false"}, {"category": "Sports", "title": "Phoenix Suns Vs. Portland Trail Blazers", "color": "#FF7043", "details": "The Phoenix Suns play Portland Trail Blazers at home", "date": "2019-01-24", "open": "false"}, {"category": "Sports", "title": "Los Angeles Lakers Vs. Minnesota Timberwolves", "color": "#FF7043", "details": "The Los Angeles Lakers play Minnesota Timberwolves at home", "date": "2019-01-24", "open": "false"}, {"category": "Sports", "title": "Orlando Magic Vs. Washington Wizards", "color": "#FF7043", "details": "The Orlando Magic play Washington Wizards at home", "date": "2019-01-25", "open": "false"}, {"category": "Sports", "title": "Brooklyn Nets Vs. New York Knicks", "color": "#FF7043", "details": "The Brooklyn Nets play New York Knicks at home", "date": "2019-01-25", "open": "false"}, {"category": "Sports", "title": "Cleveland Cavaliers Vs. Miami Heat", "color": "#FF7043", "details": "The Cleveland Cavaliers play Miami Heat at home", "date": "2019-01-25", "open": "false"}, {"category": "Sports", "title": "Chicago Bulls Vs. Los Angeles Clippers", "color": "#FF7043", "details": "The Chicago Bulls play Los Angeles Clippers at home", "date": "2019-01-25", "open": "false"}, {"category": "Sports", "title": "Houston Rockets Vs. Toronto Raptors", "color": "#FF7043", "details": "The Houston Rockets play Toronto Raptors at home", "date": "2019-01-25", "open": "false"}, {"category": "Sports", "title": "Memphis Grizzlies Vs. Sacramento Kings", "color": "#FF7043", "details": "The Memphis Grizzlies play Sacramento Kings at home", "date": "2019-01-25", "open": "false"}, {"category": "Sports", "title": "Dallas Mavericks Vs. Detroit Pistons", "color": "#FF7043", "details": "The Dallas Mavericks play Detroit Pistons at home", "date": "2019-01-25", "open": "false"}, {"category": "Sports", "title": "Milwaukee Bucks Vs. Charlotte Hornets", "color": "#FF7043", "details": "The Milwaukee Bucks play Charlotte Hornets at home", "date": "2019-01-25", "open": "false"}, {"category": "Sports", "title": "Denver Nuggets Vs. Phoenix Suns", "color": "#FF7043", "details": "The Denver Nuggets play Phoenix Suns at home", "date": "2019-01-25", "open": "false"}, {"category": "Sports", "title": "Utah Jazz Vs. Minnesota Timberwolves", "color": "#FF7043", "details": "The Utah Jazz play Minnesota Timberwolves at home", "date": "2019-01-25", "open": "false"}, {"category": "Sports", "title": "New Orleans Pelicans Vs. San Antonio Spurs", "color": "#FF7043", "details": "The New Orleans Pelicans play San Antonio Spurs at home", "date": "2019-01-26", "open": "false"}, {"category": "Sports", "title": "Memphis Grizzlies Vs. Indiana Pacers", "color": "#FF7043", "details": "The Memphis Grizzlies play Indiana Pacers at home", "date": "2019-01-26", "open": "false"}, {"category": "Sports", "title": "Boston Celtics Vs. Golden State Warriors", "color": "#FF7043", "details": "The Boston Celtics play Golden State Warriors at home", "date": "2019-01-26", "open": "false"}, {"category": "Sports", "title": "Denver Nuggets Vs. Philadelphia 76ers", "color": "#FF7043", "details": "The Denver Nuggets play Philadelphia 76ers at home", "date": "2019-01-26", "open": "false"}, {"category": "Sports", "title": "Portland Trail Blazers Vs. Atlanta Hawks", "color": "#FF7043", "details": "The Portland Trail Blazers play Atlanta Hawks at home", "date": "2019-01-26", "open": "false"}, {"category": "Sports", "title": "Chicago Bulls Vs. Cleveland Cavaliers", "color": "#FF7043", "details": "The Chicago Bulls play Cleveland Cavaliers at home", "date": "2019-01-27", "open": "false"}, {"category": "Sports", "title": "Los Angeles Clippers Vs. Sacramento Kings", "color": "#FF7043", "details": "The Los Angeles Clippers play Sacramento Kings at home", "date": "2019-01-27", "open": "false"}, {"category": "Sports", "title": "Oklahoma City Thunder Vs. Milwaukee Bucks", "color": "#FF7043", "details": "The Oklahoma City Thunder play Milwaukee Bucks at home", "date": "2019-01-27", "open": "false"}, {"category": "Sports", "title": "Houston Rockets Vs. Orlando Magic", "color": "#FF7043", "details": "The Houston Rockets play Orlando Magic at home", "date": "2019-01-27", "open": "false"}, {"category": "Sports", "title": "Dallas Mavericks Vs. Toronto Raptors", "color": "#FF7043", "details": "The Dallas Mavericks play Toronto Raptors at home", "date": "2019-01-27", "open": "false"}, {"category": "Sports", "title": "Minnesota Timberwolves Vs. Utah Jazz", "color": "#FF7043", "details": "The Minnesota Timberwolves play Utah Jazz at home", "date": "2019-01-27", "open": "false"}, {"category": "Sports", "title": "San Antonio Spurs Vs. Washington Wizards", "color": "#FF7043", "details": "The San Antonio Spurs play Washington Wizards at home", "date": "2019-01-27", "open": "false"}, {"category": "Sports", "title": "New York Knicks Vs. Miami Heat", "color": "#FF7043", "details": "The New York Knicks play Miami Heat at home", "date": "2019-01-27", "open": "false"}, {"category": "Sports", "title": "Los Angeles Lakers Vs. Phoenix Suns", "color": "#FF7043", "details": "The Los Angeles Lakers play Phoenix Suns at home", "date": "2019-01-27", "open": "false"}, {"category": "Sports", "title": "Charlotte Hornets Vs. New York Knicks", "color": "#FF7043", "details": "The Charlotte Hornets play New York Knicks at home", "date": "2019-01-28", "open": "false"}, {"category": "Sports", "title": "Indiana Pacers Vs. Golden State Warriors", "color": "#FF7043", "details": "The Indiana Pacers play Golden State Warriors at home", "date": "2019-01-28", "open": "false"}, {"category": "Sports", "title": "Boston Celtics Vs. Brooklyn Nets", "color": "#FF7043", "details": "The Boston Celtics play Brooklyn Nets at home", "date": "2019-01-28", "open": "false"}, {"category": "Sports", "title": "Memphis Grizzlies Vs. Denver Nuggets", "color": "#FF7043", "details": "The Memphis Grizzlies play Denver Nuggets at home", "date": "2019-01-28", "open": "false"}, {"category": "Sports", "title": "Los Angeles Clippers Vs. Atlanta Hawks", "color": "#FF7043", "details": "The Los Angeles Clippers play Atlanta Hawks at home", "date": "2019-01-28", "open": "false"}, {"category": "Sports", "title": "Cleveland Cavaliers Vs. Washington Wizards", "color": "#FF7043", "details": "The Cleveland Cavaliers play Washington Wizards at home", "date": "2019-01-29", "open": "false"}, {"category": "Sports", "title": "Detroit Pistons Vs. Milwaukee Bucks", "color": "#FF7043", "details": "The Detroit Pistons play Milwaukee Bucks at home", "date": "2019-01-29", "open": "false"}, {"category": "Sports", "title": "Orlando Magic Vs. Oklahoma City Thunder", "color": "#FF7043", "details": "The Orlando Magic play Oklahoma City Thunder at home", "date": "2019-01-29", "open": "false"}, {"category": "Sports", "title": "Brooklyn Nets Vs. Chicago Bulls", "color": "#FF7043", "details": "The Brooklyn Nets play Chicago Bulls at home", "date": "2019-01-29", "open": "false"}, {"category": "Sports", "title": "Houston Rockets Vs. New Orleans Pelicans", "color": "#FF7043", "details": "The Houston Rockets play New Orleans Pelicans at home", "date": "2019-01-29", "open": "false"}, {"category": "Sports", "title": "San Antonio Spurs Vs. Phoenix Suns", "color": "#FF7043", "details": "The San Antonio Spurs play Phoenix Suns at home", "date": "2019-01-29", "open": "false"}, {"category": "Sports", "title": "Los Angeles Lakers Vs. Philadelphia 76ers", "color": "#FF7043", "details": "The Los Angeles Lakers play Philadelphia 76ers at home", "date": "2019-01-29", "open": "false"}, {"category": "Sports", "title": "Boston Celtics Vs. Charlotte Hornets", "color": "#FF7043", "details": "The Boston Celtics play Charlotte Hornets at home", "date": "2019-01-30", "open": "false"}, {"category": "Sports", "title": "Miami Heat Vs. Chicago Bulls", "color": "#FF7043", "details": "The Miami Heat play Chicago Bulls at home", "date": "2019-01-30", "open": "false"}, {"category": "Sports", "title": "New York Knicks Vs. Dallas Mavericks", "color": "#FF7043", "details": "The New York Knicks play Dallas Mavericks at home", "date": "2019-01-30", "open": "false"}, {"category": "Sports", "title": "Washington Wizards Vs. Indiana Pacers", "color": "#FF7043", "details": "The Washington Wizards play Indiana Pacers at home", "date": "2019-01-30", "open": "false"}, {"category": "Sports", "title": "Minnesota Timberwolves Vs. Memphis Grizzlies", "color": "#FF7043", "details": "The Minnesota Timberwolves play Memphis Grizzlies at home", "date": "2019-01-30", "open": "false"}, {"category": "Sports", "title": "New Orleans Pelicans Vs. Denver Nuggets", "color": "#FF7043", "details": "The New Orleans Pelicans play Denver Nuggets at home", "date": "2019-01-30", "open": "false"}, {"category": "Sports", "title": "Sacramento Kings Vs. Atlanta Hawks", "color": "#FF7043", "details": "The Sacramento Kings play Atlanta Hawks at home", "date": "2019-01-30", "open": "false"}, {"category": "Sports", "title": "Portland Trail Blazers Vs. Utah Jazz", "color": "#FF7043", "details": "The Portland Trail Blazers play Utah Jazz at home", "date": "2019-01-30", "open": "false"}, {"category": "Sports", "title": "Detroit Pistons Vs. Dallas Mavericks", "color": "#FF7043", "details": "The Detroit Pistons play Dallas Mavericks at home", "date": "2019-01-31", "open": "false"}, {"category": "Sports", "title": "Orlando Magic Vs. Indiana Pacers", "color": "#FF7043", "details": "The Orlando Magic play Indiana Pacers at home", "date": "2019-01-31", "open": "false"}, {"category": "Sports", "title": "Toronto Raptors Vs. Milwaukee Bucks", "color": "#FF7043", "details": "The Toronto Raptors play Milwaukee Bucks at home", "date": "2019-01-31", "open": "false"}, {"category": "Sports", "title": "San Antonio Spurs Vs. Brooklyn Nets", "color": "#FF7043", "details": "The San Antonio Spurs play Brooklyn Nets at home", "date": "2019-01-31", "open": "false"}, {"category": "Sports", "title": "Golden State Warriors Vs. Philadelphia 76ers", "color": "#FF7043", "details": "The Golden State Warriors play Philadelphia 76ers at home", "date": "2019-01-31", "open": "false"}, {"category": "Sports", "title": "Los Angeles Clippers Vs. Los Angeles Lakers", "color": "#FF7043", "details": "The Los Angeles Clippers play Los Angeles Lakers at home", "date": "2019-01-31", "open": "false"}


      ],
      nba:[{"category": "Sports", "title": "SAC", "color": "color", "details": "The SAC play LAL at home", "date": "2018-07-02", "open": "false"}, {"category": "Sports", "title": "ATL", "color": "color", "details": "The ATL play SAS at home", "date": "2018-07-03 19:00:00.0", "open": "false"}],
      items: [
        {
          src: 'https://cdn.vuetifyjs.com/images/carousel/squirrel.jpg'
        },
        {
          src: 'https://cdn.vuetifyjs.com/images/carousel/sky.jpg'
        },
        {
          src: 'https://cdn.vuetifyjs.com/images/carousel/bird.jpg'
        },
        {
          src: 'https://cdn.vuetifyjs.com/images/carousel/planet.jpg'
        }
      ]
    }),
    computed: {
      // convert the list of events into a map of lists keyed by date
      eventsMap () {
        const map = {}
        this.events.forEach(e => (map[e.date] = map[e.date] || []).push(e))
        return map
      },
      filteredEvents () {
        const map = {}
        this.events.forEach(e => (map[e.category] = map[e.category] || []).push(e))
        return map
      },
      filteredEvents2: function () {
        var self = this
        return self.events.filter(function (event) {
          return events.category.indexOf("1") !== -1
        })
      }
    },
    mounted() {
    let recaptchaScript = document.createElement('script')
    recaptchaScript.setAttribute('src', './js/index.js')
    document.head.appendChild(recaptchaScript)

    let recaptchaScript2 = document.createElement('script')
    recaptchaScript2.setAttribute('src', 'https://sdk.amazonaws.com/js/aws-sdk-2.48.0.min.js')
    document.head.appendChild(recaptchaScript2)

    let recaptchaScript3 = document.createElement('script')
    recaptchaScript3.setAttribute('src', './aws-lex-audio.js')
    document.head.appendChild(recaptchaScript3)
    },
    methods: {
      open (event) {
        alert(event.title)
      }, 
      greet: function (event) {
        // `this` inside methods points to the Vue instance

        
        //alert('Hello ' + this.name + '!')
        // `event` is the native DOM event
        if (event) {
          this.events.push({title: this.titleBox, color: '#FF4377' , details: this.descriptionBox, category: this.categoryBox, date: this.dateBox});
        }
      },
      greet2: function (event) {
        this.events.push({title: 'New Year2', color: '#FF4377' , details: 'Eat chocolate until you pass out', date: '2019-01-21'});
      },
      search: function () {
         alert(this.range.start);
         console.log(this.range.end);
      }
    }
  }

</script>




<style lang="stylus" scoped>
  .my-event {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    border-radius: 2px;
    background-color: #1867c0;
    color: #ffffff;
    border: 1px solid #1867c0;
    width: 100%;
    font-size: 12px;
    padding: 3px;
    cursor: pointer;
    margin-bottom: 1px;
  }

ul {
  list-style-type: none;

  }
</style>