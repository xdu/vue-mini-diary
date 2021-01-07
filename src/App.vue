<template>
  <div id="app">
    <!-- <img src="local-resource://C:/Users/xdu/Workspace/hexo/source/images/2020/12/06/59822a38-a487-4b2d-a5c4-ab12b7f60641.png"/> -->
    <div id="left">
      <Calendar v-bind:datesWithNote="currentMonth" v-on:dayclick="loadNote" />
    </div>
    <div id="right">
      <Editor v-model="note" />
    </div>
  </div>
</template>

<script>
import fs from 'fs'
import path from 'path'
import moment from 'moment'
import 'core-js'

import Editor from "./components/Editor"
import Calendar from "./components/Calendar"

const postPath = "C:\\Users\\xdu\\Workspace\\hexo\\source\\_posts"

export default {
  name: "App",
  components: {
    Editor,
    Calendar
  },

  mounted() {
    
  },

  created() {
    console.log("Parent created: " + this.currentMonth.length)
    this.getNoteDates()
  },

  updated() {
    console.log("Parent data changed : " + this.currentMonth.length)
  },

  data: () => {
    return {
      currentMonth: [],
      note: ""
    }
  },

  methods: {

    loadNote: function(dayId) {
      
      let prefix = dayId.replace(/-/g, "")
      let vm = this

      fs.readdir(postPath, (errDir, files) => {

        let match = ""
        for (let i = 0; i < files.length; i ++) {
          if (files[i].startsWith(prefix)) {
            match = files[i]
            break
          }
        }

        if (match) {
          console.log("Load file " + match)
          fs.readFile(path.join(postPath, match), (errReadFile, contents) => {
            vm.note = contents.toString()
          })
        }
      })
    },

    getNoteDates() {

      /*
      let month2 = new String(month)
      if (month2.length == 1)
        month2 = "0" + month2

      let pattern = year + month2
    */
      let dates = new Array()

      fs.readdir(postPath, (err, files) => {
        if (err) throw err
        for (let i = 0; i < files.length; i ++) {

          if (files[i].match(/^[0-9]{6}/gi)) {
            let strDate = files[i].substring(0, 8)
            let dt = moment(strDate, 'YYYYMMDD').toDate()

            dates.push(dt)
            console.log(dt.toString())
          }
        }

        this.currentMonth = dates

      })
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin-top: 60px;
}
#right {
  margin-left: 320px;
  overflow: hidden;
  margin: 20px;
}
#left {
  width: 300px;
  float: left;
}
</style>
