<template>
<div id="app">
  <v-app id="inspire">
    <v-navigation-drawer
      permanent
      :clipped="$vuetify.breakpoint.mdAndUp"
      app
      v-model="drawer"
    >

    <h1>{{selected}}</h1>


   



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
      <div>
          <v-btn depressed large color="rgba(255, 0, 0, 0.5)" v-on:click="greet">Add Event</v-btn>
        </div>


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

      <v-btn v-on:click="search" flat icon color="pink">
        <v-icon>favorite</v-icon>
      </v-btn>
      
    </v-toolbar>


<v-content>

<v-carousel style="height:100%">
      <v-carousel-item
      >
      <v-dialog v-model="dialog" fullscreen hide-overlay transition="dialog-bottom-transition">
        <template v-slot:activator="{ on }">
          <v-btn color="primary" dark v-on="on">Change Filter</v-btn>
        </template>
        <v-card>
          <v-toolbar dark color="primary">
            <v-btn icon dark @click="dialog = false">
              <v-icon>close</v-icon>
            </v-btn>
            <v-toolbar-title>Filter</v-toolbar-title>
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

      </v-carousel-item>
      <v-carousel-item
      >

      <WeekChart :events="events"/>
      
      </v-carousel-item>
    </v-carousel>

 

        
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
        {"category": "Sports", "title": "Sacramento Kings Vs. Los Angeles Lakers", "color": "#FF7043", "details": "The Sacramento Kings play Los Angeles Lakers at home", "date": "2018-07-02", "open": "false"}, {"category": "Sports", "title": "Atlanta Hawks Vs. San Antonio Spurs", "color": "#FF7043", "details": "The Atlanta Hawks play San Antonio Spurs at home", "date": "2018-07-03", "open": "false"}, {"category": "Sports", "title": "Los Angeles Lakers Vs. Miami Heat", "color": "#FF7043", "details": "The Los Angeles Lakers play Miami Heat at home", "date": "2018-07-03", "open": "false"}, {"category": "Sports", "title": "Utah Jazz Vs. Memphis Grizzlies", "color": "#FF7043", "details": "The Utah Jazz play Memphis Grizzlies at home", "date": "2018-07-03", "open": "false"}, {"category": "Sports", "title": "Sacramento Kings Vs. Golden State Warriors", "color": "#FF7043", "details": "The Sacramento Kings play Golden State Warriors at home", "date": "2018-07-03", "open": "false"}, {"category": "Sports", "title": "Golden State Warriors Vs. Los Angeles Lakers", "color": "#FF7043", "details": "The Golden State Warriors play Los Angeles Lakers at home", "date": "2018-07-05", "open": "false"}, {"category": "Sports", "title": "Sacramento Kings Vs. Miami Heat", "color": "#FF7043", "details": "The Sacramento Kings play Miami Heat at home", "date": "2018-07-05", "open": "false"}, {"category": "Sports", "title": "San Antonio Spurs Vs. Memphis Grizzlies", "color": "#FF7043", "details": "The San Antonio Spurs play Memphis Grizzlies at home", "date": "2018-07-05", "open": "false"}, {"category": "Sports", "title": "Utah Jazz Vs. Atlanta Hawks", "color": "#FF7043", "details": "The Utah Jazz play Atlanta Hawks at home", "date": "2018-07-05", "open": "false"}, {"category": "Sports", "title": "Indiana Pacers Vs. Houston Rockets", "color": "#FF7043", "details": "The Indiana Pacers play Houston Rockets at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "New Orleans Pelicans Vs. Toronto Raptors", "color": "#FF7043", "details": "The New Orleans Pelicans play Toronto Raptors at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "Orlando Magic Vs. Brooklyn Nets", "color": "#FF7043", "details": "The Orlando Magic play Brooklyn Nets at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "Charlotte Hornets Vs. Oklahoma City Thunder", "color": "#FF7043", "details": "The Charlotte Hornets play Oklahoma City Thunder at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "Detroit Pistons Vs. Milwaukee Bucks", "color": "#FF7043", "details": "The Detroit Pistons play Milwaukee Bucks at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "Philadelphia 76ers Vs. Boston Celtics", "color": "#FF7043", "details": "The Philadelphia 76ers play Boston Celtics at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "Washington Wizards Vs. Cleveland Cavaliers", "color": "#FF7043", "details": "The Washington Wizards play Cleveland Cavaliers at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "Phoenix Suns Vs. Dallas Mavericks", "color": "#FF7043", "details": "The Phoenix Suns play Dallas Mavericks at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "Minnesota Timberwolves Vs. Denver Nuggets", "color": "#FF7043", "details": "The Minnesota Timberwolves play Denver Nuggets at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "Golden State Warriors Vs. Los Angeles Clippers", "color": "#FF7043", "details": "The Golden State Warriors play Los Angeles Clippers at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "Utah Jazz Vs. Portland Trail Blazers", "color": "#FF7043", "details": "The Utah Jazz play Portland Trail Blazers at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "Indiana Pacers Vs. San Antonio Spurs", "color": "#FF7043", "details": "The Indiana Pacers play San Antonio Spurs at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "New Orleans Pelicans Vs. Miami Heat", "color": "#FF7043", "details": "The New Orleans Pelicans play Miami Heat at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "Atlanta Hawks Vs. New York Knicks", "color": "#FF7043", "details": "The Atlanta Hawks play New York Knicks at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "Brooklyn Nets Vs. Oklahoma City Thunder", "color": "#FF7043", "details": "The Brooklyn Nets play Oklahoma City Thunder at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "Sacramento Kings Vs. Phoenix Suns", "color": "#FF7043", "details": "The Sacramento Kings play Phoenix Suns at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "Memphis Grizzlies Vs. Detroit Pistons", "color": "#FF7043", "details": "The Memphis Grizzlies play Detroit Pistons at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "Cleveland Cavaliers Vs. Chicago Bulls", "color": "#FF7043", "details": "The Cleveland Cavaliers play Chicago Bulls at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "Denver Nuggets Vs. Boston Celtics", "color": "#FF7043", "details": "The Denver Nuggets play Boston Celtics at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "Los Angeles Lakers Vs. Philadelphia 76ers", "color": "#FF7043", "details": "The Los Angeles Lakers play Philadelphia 76ers at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "Toronto Raptors Vs. Minnesota Timberwolves", "color": "#FF7043", "details": "The Toronto Raptors play Minnesota Timberwolves at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "San Antonio Spurs Vs. Washington Wizards", "color": "#FF7043", "details": "The San Antonio Spurs play Washington Wizards at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "Miami Heat Vs. Charlotte Hornets", "color": "#FF7043", "details": "The Miami Heat play Charlotte Hornets at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "Atlanta Hawks Vs. Portland Trail Blazers", "color": "#FF7043", "details": "The Atlanta Hawks play Portland Trail Blazers at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "Milwaukee Bucks Vs. Dallas Mavericks", "color": "#FF7043", "details": "The Milwaukee Bucks play Dallas Mavericks at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "Houston Rockets Vs. Golden State Warriors", "color": "#FF7043", "details": "The Houston Rockets play Golden State Warriors at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "New York Knicks Vs. Utah Jazz", "color": "#FF7043", "details": "The New York Knicks play Utah Jazz at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "Orlando Magic Vs. Memphis Grizzlies", "color": "#FF7043", "details": "The Orlando Magic play Memphis Grizzlies at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "New York Knicks Vs. Utah Jazz", "color": "#FF7043", "details": "The New York Knicks play Utah Jazz at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "Orlando Magic Vs. Memphis Grizzlies", "color": "#FF7043", "details": "The Orlando Magic play Memphis Grizzlies at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "Los Angeles Clippers Vs. Sacramento Kings", "color": "#FF7043", "details": "The Los Angeles Clippers play Sacramento Kings at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "Chicago Bulls Vs. Los Angeles Lakers", "color": "#FF7043", "details": "The Chicago Bulls play Los Angeles Lakers at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "Detroit Pistons Vs. New Orleans Pelicans", "color": "#FF7043", "details": "The Detroit Pistons play New Orleans Pelicans at home", "date": "2018-07-09", "open": "false"}, {"category": "Sports", "title": "Oklahoma City Thunder Vs. Toronto Raptors", "color": "#FF7043", "details": "The Oklahoma City Thunder play Toronto Raptors at home", "date": "2018-07-09", "open": "false"}, {"category": "Sports", "title": "Cleveland Cavaliers Vs. Indiana Pacers", "color": "#FF7043", "details": "The Cleveland Cavaliers play Indiana Pacers at home", "date": "2018-07-09", "open": "false"}, {"category": "Sports", "title": "Philadelphia 76ers Vs. Washington Wizards", "color": "#FF7043", "details": "The Philadelphia 76ers play Washington Wizards at home", "date": "2018-07-09", "open": "false"}, {"category": "Sports", "title": "Boston Celtics Vs. Charlotte Hornets", "color": "#FF7043", "details": "The Boston Celtics play Charlotte Hornets at home", "date": "2018-07-09", "open": "false"}, {"category": "Sports", "title": "Dallas Mavericks Vs. Golden State Warriors", "color": "#FF7043", "details": "The Dallas Mavericks play Golden State Warriors at home", "date": "2018-07-09", "open": "false"}, {"category": "Sports", "title": "Denver Nuggets Vs. Milwaukee Bucks", "color": "#FF7043", "details": "The Denver Nuggets play Milwaukee Bucks at home", "date": "2018-07-09", "open": "false"}, {"category": "Sports", "title": "Phoenix Suns Vs. Orlando Magic", "color": "#FF7043", "details": "The Phoenix Suns play Orlando Magic at home", "date": "2018-07-09", "open": "false"}, {"category": "Sports", "title": "Minnesota Timberwolves Vs. Brooklyn Nets", "color": "#FF7043", "details": "The Minnesota Timberwolves play Brooklyn Nets at home", "date": "2018-07-09", "open": "false"}, {"category": "Sports", "title": "Houston Rockets Vs. Los Angeles Clippers", "color": "#FF7043", "details": "The Houston Rockets play Los Angeles Clippers at home", "date": "2018-07-09", "open": "false"},
        {"category": "Sports", "title": "Portland Trail Blazers Vs. San Antonio Spurs", "color": "#FF7043", "details": "The Portland Trail Blazers play San Antonio Spurs at home", "date": "2018-07-10", "open": "false"}, {"category": "Sports", "title": "Miami Heat Vs. Utah Jazz", "color": "#FF7043", "details": "The Miami Heat play Utah Jazz at home", "date": "2018-07-10", "open": "false"}


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