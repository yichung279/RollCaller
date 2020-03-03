<template lang="pug">
#app.container
  video#qr-video.item

  p {{ scannerStatus }}

  .ui.form.item
    .inline.fields
      .field
        input(type="text",placeholder="Name", v-model='name')
      .field
        input(type="text",placeholder="Id", v-model='id')
      .field
        .ui.submit.button(@click='addStudent(id, name)') Submit

  #table.scroll.item
    table.ui.celled.table
      thead
        tr
          th(v-for='header in headers') {{ header }}
      tbody
        tr(v-for='student in table')
          td(v-for='content in student') {{ content }}

  .item.button-container
    button.ui.button.large(@click='cleanTable') 清除檔案
    button.ui.button.large(@click='saveFile') 儲存檔案

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

    this.table.forEach(row=>{
      this.students[row[1]] = row[0]
    })

    const video = document.getElementById('qr-video');
    const scanner = new QrScanner(video, qrcode => this.handleQRCode(qrcode));
    scanner.start();
  },

	data(){return{
    id: "",
    name: "",
    headers: ['Name', 'Id', 'Time'],
    students: {},
    table: [],
    scannerStatus: "未發現 QRCode",
    timer: null,
	}},

  methods: {
    handleQRCode(qrcode) {
      // remove utf8-bom
      if (qrcode.charCodeAt(0) === 0xFEFF)
        qrcode = qrcode.substr(1);

      let inputs = qrcode.split('/@/')

      if(inputs[0]!='qr-check'){
        this.setScannerStatus('非合法QRcode')
        return
      }

      let id = inputs[1].substring(0, 15)
      let name = inputs[2].substring(0, 15)

      this.addStudent(id, name)
    },

    addStudent(id, name){
      let timeString = new Date().toLocaleTimeString()

      if(this.students[id]!=undefined){
        this.setScannerStatus('已加入id:'+id)
        return
      }

      this.students[id]={"name": name}
      this.table.push([name, id, timeString])
      localStorage.studentTable = JSON.stringify(this.table)
    },

    setScannerStatus(message, wait=500){
      this.scannerStatus = message

      if(this.timer!=null)
        window.clearTimeout(this.timer)

      this.timer = window.setTimeout(()=>{
        this.scannerStatus = "未發現 QRCode"
      }, wait)
    },

    cleanTable(){
      this.students = {}
      this.table = []
      localStorage.removeItem('studentTable')
    },

    saveFile(){
			let dayString = new Date().toISOString().substring(0, 10)

      let csvFormat = this.buildStudentCSV()
      let filename = `${dayString}簽到表.csv`
      let type = "text/plain;charset=utf-8"

			let file = new File(csvFormat, filename, {type: type})

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
  //<!-- overflow: hidden -->
</style>

<style lang="sass" scoped>
.container
  padding: 3vh
  display: flex
  align-items: center
  flex-direction: column
  justify-content: space-around

.item
  margin: 0.5em

video
  max-height: 200px

.scroll
  overflow: scroll

#table
  width: 40em
  flex-grow: 3

.button-container
  width: 30em
  display: flex
  justify-content: space-evenly
</style>
<!--
  vi:et:sw=2:ts=2
-->
