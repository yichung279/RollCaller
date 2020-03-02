<template lang="pug">
#app.container
  video#preview
</template>

<script>
import 'semantic-ui-offline/semantic.min.css'
import Instascan from 'instascan'

export default {

  mounted() {
		let scanner = new Instascan.Scanner({ video: document.getElementById('preview') })

		scanner.addListener('scan', function (content) {
			console.log(content);
		})

		Instascan.Camera.getCameras().then(function (cameras) {
			if (cameras.length > 0) {
				scanner.start(cameras[0]);
			} else {
				console.error('No cameras found.');
			}
		}).catch(function (e) {
			console.error(e)
		})

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
