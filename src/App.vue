<template>
  <div id="app">
    <!-- <img src="local-resource://C:/Users/xdu/Workspace/hexo/source/images/2020/12/06/59822a38-a487-4b2d-a5c4-ab12b7f60641.png"/> -->
    <div id="left">
      <Calendar v-bind:datesWithNote="datesWithNote" v-on:dayclick="loadNote" />
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
import chokidar from 'chokidar'

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
    console.log("Parent created: " + this.datesWithNote.length)

    this.watcher = chokidar.watch(postPath, {
      persistent: true,
      ignoreInitial: true,
      depth: 0,
      awaitWriteFinish: true
    })
    this.watcher.on('change', path => { console.log("file change " + path) })
    this.watcher.on('add', path => { console.log("file add " + path)})
    this.watcher.on('unlink', path => { console.log("file delete " + path)})
    
    this.getDatesWithNote()
  },

  updated() {
    console.log("Parent data changed : " + this.datesWithNote.length)
  },

  async destroyed() {
    if (this.watcher) {
      await this.watcher.close()
    }
  },

  data: () => {
    return {
      datesWithNote: [],
      watcher: null,
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

    /**
     * Load the posts and return a list of dates.
     */
    getDatesWithNote() {

      let arry = new Array()

      fs.readdir(postPath, (err, files) => {
        // Folder reading error
        if (err) throw err

        for (let i = 0; i < files.length; i ++) {

          // Take only the filename staring with 8 numbers, such as 20201020
          if (files[i].match(/^[0-9]{8}/gi)) {

            // Convert the file prefix to a date
            let strDate = files[i].substring(0, 8)
            let dt = moment(strDate, 'YYYYMMDD').toDate()

            arry.push(dt)
          }
        }

        this.datesWithNote = arry
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
