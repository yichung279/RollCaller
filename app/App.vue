<template lang="pug">
#app.container
  h1.ui.header QRcode Generater
  form.ui.form
    div.field.item
      label Name
      div.ui.input
        input(type="text",name="name",placeholder="Name...",v-model="name")

    div.field.item

      label Id
      div.ui.input
        input(type="text",name="id",placeholder="Id...",v-model="id")

  v-qrcode.qrcode(:text="qrcode_content")

</template>

<script>
import 'semantic-ui-offline/semantic.min.css'

export default {

  components: {
    "v-qrcode": require('vue-qrcode-component').default
  },

  data() { return {
    id: "",
    name: "",
  }},

  computed: {
    qrcode_content() {
      return `qr-check/@/${this.id}/@/${this.name}`
    }
  },

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

form
  width: 30em

.qrcode
  margin: 5em
</style>

<!--
  vi:et:sw=2:ts=2
-->
