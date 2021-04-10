<template>
  <div class="container">
    <b-form-textarea
      id="textarea"
      v-model="rawHtml"
      placeholder=""
      rows="3"
      max-rows="6"
      @keydown.tab.prevent="tabber($event)"
    ></b-form-textarea>
    <div class="html-container" >
      <div id="userImg" v-html="rawHtml"></div>
    </div>
    <div class="html-container">
      <canvas id="targetCanvas" width="300" height="300"></canvas>
    </div>
    <b-button 
      v-on:click="save"
      variant="success"
      >Save</b-button>
  </div>
</template>

<script>
import html2canvas from 'html2canvas';
export default {
  
  data(){
    return({
      rawHtml: "<div class='hej'>\n\t\n</div>\n<style>\n.hej{\n\tbackground:black;width:100%;height:100%\n}\n</style>"
    })
  },
  mounted(){
    let c = document.getElementById("targetCanvas");
    let ctx = c.getContext("2d");
    ctx.fillStyle = 'blue';
    ctx.fillRect(10, 10, 100, 100);
  },
  methods: {
    tabber (event) {
      if (event) {
        event.preventDefault()
        let start = event.target.selectionStart
        let startText = this.rawHtml.slice(0, event.target.selectionStart)
        let endText = this.rawHtml.slice(event.target.selectionStart)
        this.rawHtml = `${startText}\t${endText}`
        this.$nextTick(() => {
          event.target.selectionStart= start +1
          event.target.selectionEnd = start +1
        })
        
      }
    },
    save(){
      html2canvas(document.getElementById('userImg')).then(canvas => {
        let ctx = canvas.getContext('2d')
        let targetCtx = document.getElementById("targetCanvas").getContext('2d')
        let color = ctx.getImageData(0,0,300,300);
        let targetColor = targetCtx.getImageData(0,0,300,300);
        console.log(compareImages(color, targetColor))
        
      })
    }
  }
}
const compareImages = (img1, img2) => {
  if(img1.data.length != img2.data.length)
       return false;
    let total = img1.data.length/4;
    let matching = 0;
    for(var i = 0; i < img1.data.length; i+=4){
      if(img1.data[i] === img2.data[i] && img1.data[i+1] === img2.data[i+1] && img1.data[i+2] === img2.data[i+2]){
        matching++;
      }   
    }
   return ((matching/total)*100).toPrecision(4); 
}
</script>

<style lang="scss">
  .html-container{
    border:1px solid black;
    width:300px;
    height:300px;
  }
  #userImg{
    width:300px;
    height:300px;
  }
  
</style>
