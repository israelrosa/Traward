<template>
  <v-app>
    <v-app-bar app color="white" elevation="2" class="px-4">
      <v-text-field
        placeholder="What are you working on?"
        dense
        hide-details
        :disabled="this.status"
        class="mr-5"
        v-on:change="setTitle"

      ></v-text-field> 
      <v-btn
        text
        class="mx-5"
      >{{formattedElapsedTime}}</v-btn> 
      <v-btn
        fab
        small
        class="ml-5"
        elevation="0"
        color="purple"
        @click="changeStatus"
      >
        <v-icon color="white">mdi-play</v-icon>
      </v-btn>
      
    </v-app-bar>
    <v-main class="background" >
      <v-container class="pa-0 ma-0 background" fluid>
        <v-list>
          <v-list-item-group v-for="section in arraySections" :key="section[0]">
            <v-card>
              <header id="card-header">
                <h2>{{section[0]}}</h2>
                <v-btn
                  text
                  class="ml"
                >0:00:00</v-btn> 
              </header>
              <template
                v-for="(item, index) in section[1]"
              >
                <v-list-item
                  :key="item.title"
                >
                  <template>
                    <v-btn outlined icon class="mr-4"> 
                      1
                    </v-btn>
                    
                    <v-list-item-content>
                      <v-list-item-title v-text="item.title"></v-list-item-title>
                    </v-list-item-content>
                    <v-btn
                      text
                      class="ml"
                    >0:00:00</v-btn> 
                    <v-btn icon color="primary" disabled>
                      <v-icon>mdi-play</v-icon>
                    </v-btn>
                    <v-btn icon color="primary" disabled>
                      <v-icon>mdi-dots-vertical</v-icon>
                    </v-btn>
                  </template>
                </v-list-item>
                <v-divider :key="index"></v-divider>
              </template>
            </v-card>
          </v-list-item-group>  
        </v-list>
      </v-container>
    </v-main>
  </v-app>
    
</template>

<script>

import moment from 'moment';

export default {
  name: 'Home',
  data () {
    return {
      sections: {},
      elapsedTime: 0,
      timer: undefined,
      status: false,
      title: '',
    }
  },

  computed: {
    formattedElapsedTime() {
      const date = new Date(null);
      date.setSeconds(this.elapsedTime / 1000);
      const utc = date.toUTCString();
      return utc.substr(utc.indexOf(":") - 2, 8);
    },
    arraySections() {
      return Object.entries(this.sections);
    }
  },
  methods: {
    start() {
      this.status = true;
      this.timer = setInterval(() => {
        this.elapsedTime += 1000;
      }, 1000);
    },
    stop() {
      clearInterval(this.timer);
      this.status = false;
      const data = moment(new Date()).format('DD-MM-YYYY');
      if(Reflect.has(this.sections, data)) {
        const index = this.sections[data].findIndex(section => section.title === this.title);
        if(index !== -1) {
          this.sections[data][index].items.push({timer: this.elapsedTime});
        }else {
          this.sections[data].push([{title: this.title, items: [{timer: this.elapsedTime}]}]);
        }
      } else {
        Reflect.set(this.sections, data, [{title: this.title, items: [{timer: this.elapsedTime}]}]);
      }
      this.elapsedTime = 0;
    },
    changeStatus() {
      this.status? this.stop() : this.start();
    },
    setTitle(e) {
      console.log(e);
    },
    watch: {
      sections: {
        handler(e) {
          console.log(e);
        },
        deep: true,
      }
    }
  }

}
</script>

<style scoped>
  #card-header {
    display: flex;
    flex-direction: row;
    padding:20px 16px;
    justify-content: space-between;
    align-items: center;
  }
</style>