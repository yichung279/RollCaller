<template lang="pug">
#app.container
  video#qr-video.item(width='100%')

  #table.scroll.item
    table.ui.celled.table
      thead
        tr
          th(width='50%') Name
          th(width='50%') Id
      tbody
        tr(v-for='student in table')
          td(v-for='content in student') {{ content }}

  button.ui.button.massive.item 產生檔案

</template>

<script>
import 'semantic-ui-offline/semantic.min.css'
import QrScanner from './qr-scanner.min.js'
QrScanner.WORKER_PATH = './qr-scanner-worker.min.js'

export default {

  mounted(){
    const video = document.getElementById('qr-video');

    const scanner = new QrScanner(video, content => this.addContent(content));
    scanner.start();

    document.getElementById('table').style.width = video.offsetWidth + 'px'
  },

	data(){return{
    students: {},
    table: [['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b'],['a','b']],
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
        this.table.push([inputs[1], inputs[2]])
      }
    }
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
  margin: 2em
  max-width: 600px

.scroll
  overflow: scroll

</style>
<!--
  vi:et:sw=2:ts=2
-->
