<template>
  <div class="container">
    <editor 
      v-model="rawHtml"
    />
    <iframe id="iframeId" class="html-container" width="300" height="300"/>
    <div class="html-container">
      <canvas id="targetCanvas" width="300" height="300"></canvas>
    </div>
    <b-button 
      v-on:click="save"
      variant="success"
      >Save</b-button>
    <div style="" id="html"></div>
  </div>
</template>

<script>
import html2canvas from 'html2canvas';
import Editor from '../components/Editor';
export default {
  components: { Editor },
  data(){
    return({
      rawHtml: "<div class='hej'>\n\t\n</div>\n<style>\n.hej{\n\tbackground:green;\n\twidth:100%;\n\theight:100%\n}\n</style>"
    })
  },
  mounted(){
    // Create solution
    let c = document.getElementById("targetCanvas");
    let ctx = c.getContext("2d");
    ctx.fillStyle = 'white';
    ctx.fillRect(0, 0, 300, 300);

    // Update from user-code
    this.updateFromCode(this.rawHtml)
  },
  methods: {
    updateFromCode(text){
      var parser = new DOMParser();
      var parsedDocument = parser.parseFromString(text, 'text/html');
      var newHTML = parsedDocument.documentElement.outerHTML;
      var doc = document.getElementById('iframeId').contentWindow.document;
      doc.open();
      doc.write(newHTML);
      doc.close();
    },
    save(){
      html2canvas(document.getElementById('iframeId').contentDocument.body).then(canvas => {
        console.log(canvas)
        let ctx = canvas.getContext('2d')
        let targetCtx = document.getElementById("targetCanvas").getContext('2d')
        let color = ctx.getImageData(0,0,300,300);
        let targetColor = targetCtx.getImageData(0,0,300,300);
        console.log(compareImages(color, targetColor))
        
      })
    }
  },
  watch:{
    rawHtml(newVal, oldVal){
      this.updateFromCode(newVal)
    }
  }
}
const compareImages = (img1, img2) => {
  if(img1.data.length != img2.data.length)
       return false;
    let total = img1.data.length/4;
    console.log(img1.data.length, img2.data.length)
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
    outline:1px solid black;
    width:300px;
    height:300px;
    margin-top:10px;
  }
  #userImg{
    width:300px;
    height:300px;
  }
  #iframeId{
    border:none
  }
  
</style>
