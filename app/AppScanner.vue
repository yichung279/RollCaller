<template lang="pug">
#app.container
  video#qr-video
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
  },

	data(){return{
    table: {},
	}},

  methods: {
    addContent(content) {
      // remove utf8-bom
			if (content.charCodeAt(0) === 0xFEFF) {
				content = content.substr(1);
			}

      let student = content.split('/')

      console.log(student)
      if(student[0]!='qr-check')
        return

      if(this.table[student[1]]==undefined){
        this.table[student[1]]={"name": student[2]}
        console.log(this.table)
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
  padding: 5vh
  display: flex
  align-items: center
  flex-direction: column

</style>

<!--
  vi:et:sw=2:ts=2
-->
