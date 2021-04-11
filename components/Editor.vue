<template>
  <b-form-textarea
      id="textarea"
      v-model="text"
      placeholder=""
      rows="9"
      max-rows="27"
      @keydown.tab.prevent="tabber($event)"
    ></b-form-textarea>
</template>

<script>
export default {
    props:['value'],
    methods:{
        tabber (event) {
            if (event) {
                event.preventDefault()
                let start = event.target.selectionStart
                let startText = this.text.slice(0, event.target.selectionStart)
                let endText = this.text.slice(event.target.selectionStart)
                this.text = `${startText}\t${endText}`
                this.$nextTick(() => {
                    event.target.selectionStart= start +1
                    event.target.selectionEnd = start +1
                })
            }
        },
    },
    computed: {
        text: {
            get() { return this.value },
            set(text) {this.$emit('input', text)}
        }
    }
    

}
</script>

<style>

</style>