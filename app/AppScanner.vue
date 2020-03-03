<template lang="pug">
#app.container
  video#qr-video.item(width='100%')

  #table.scroll.item
    table.ui.celled.table
      thead
        tr
          th(v-for='header in headers') {{ header }}
      tbody
        tr(v-for='student in table')
          td(v-for='content in student') {{ content }}

  .item.button-container
    button.ui.button.massive(@click='cleanTable') 清除檔案
    button.ui.button.massive(@click='saveFile') 儲存檔案

</template>

<script>
import 'semantic-ui-offline/semantic.min.css'
import FileSaver from 'file-saver'
import QrScanner from './qr-scanner.min.js'
QrScanner.WORKER_PATH = './qr-scanner-worker.min.js'

export default {

  mounted(){
    if(localStorage.studentTable!=undefined)
      this.table = JSON.parse(localStorage.studentTable)

    const video = document.getElementById('qr-video');

    const scanner = new QrScanner(video, content => this.addContent(content));
    scanner.start();

    document.getElementById('table').style.width = video.offsetWidth + 'px'
  },

	data(){return{
    headers: ['Name', 'Id', 'Time'],
    students: {},
    table: [],
	}},

  methods: {
    addContent(content) {
      // remove utf8-bom
			if (content.charCodeAt(0) === 0xFEFF) {
				content = content.substr(1);
			}

      let inputs = content.split('/@/')

      if(inputs[0]!='qr-check')
        return

      if(this.students[inputs[1]]==undefined){
        this.students[inputs[1]]={"name": inputs[2]}
        let d = new Date()
        let timeString = d.toLocaleTimeString()
        this.table.push([inputs[1], inputs[2], timeString])
        localStorage.studentTable = JSON.stringify(this.table)
      }
    },

    cleanTable(){
      this.table = []
      localStorage.removeItem('studentTable')
    },

    saveFile(){

			let today = new Date()
			let dayString = today.toISOString().substring(0, 10)

      let csvFormat = this.buildStudentCSV()

			let file = new File(csvFormat, `${dayString}簽到表.csv`, {type: "text/plain;charset=utf-8"})
			FileSaver.saveAs(file)
    },

    buildStudentCSV(){
      let csv = []

      csv.push(this.headers.join(',')+'\n')

      this.table.forEach(student => {
        csv.push(student.join(',')+'\n')
      })

      return csv
    },

  }

}
</script>

<style lang="sass">
body,#app
  height: 100%
  overflow: hidden
</style>

<style lang="sass" scoped>
.container
  padding: 3vh
  display: flex
  align-items: center
  flex-direction: column

.item
  margin: 1em
  max-width: 400px

.scroll
  overflow: scroll

.button-container
  width: 100%
  display: flex
  justify-content: space-around

</style>
<!--
  vi:et:sw=2:ts=2
-->
