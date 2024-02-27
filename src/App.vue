<template>
  <div class="flip-container">
    <div class="fliphtml5">
      <iframe :src="flipbookData[book_index]['url']" frameborder="0" scrolling="no"></iframe>
    </div>
  </div>
  <div class="description">
    <h2 class="flipbookName">{{ flipbookData[book_index]["name"] }}</h2>
    <p class="date">{{ `Published on ${formatDate(flipbookData[book_index]['date_released'])}` }}</p>
    <p class="flipbookDesc">{{ flipbookData[book_index]["description"] }}</p>
  </div>
  <h3>Read More</h3>
  <div class="releases">
    <!-- <div class="left-gradient"></div> -->
      <div class="releases-container">
        <div v-for="(flipbook, index) in flipbookData" :key="index">
          <div class="preview" @click="chooseIndex(index)">
            <img 
            class="previewImg"
            :src='getImgUrl(flipbook["img"])' 
            :alt="flipbook['name']"
            />
            <p class="preview-name">{{ flipbook['name'] }}</p>
            <p class="date-released">{{ formatDate(flipbook['date_released']) }}</p>
          </div>
        </div>
      </div>
    <!-- <div class="right-gradient"></div> -->
  </div>
</template>

<script>
import FlipbookData from "./FlipbookData.json";

export default {
  name: 'App',
  components: {
    
  },
  data(){
    return {
      flipbookData: [],
      book_index: 0,
    }
  },
  created(){
    this.flipbookData = FlipbookData.flipbookData;
    console.log(this.flipbookData[0]["img"])
  },
  methods:{
      getImgUrl(img){
        let images = require.context('./assets/', false, /\.png$/);
        return images('./' + img + ".png");
      },
      incrementBookIndex(){
        if (this.book_index === this.flipbookData.length - 1){
          this.book_index = 0;
        } else {
          this.book_index += 1;
        }
      },
      decrementBookIndex(){
        if (this.book_index === 0){
          this.book_index = this.flipbookData.length - 1;
        } else {
          this.book_index -= 1;
        }
      },
      chooseIndex(index){
        this.book_index = index;
      },
      formatDate(date){
        return Intl.DateTimeFormat('en-US', {
                month: 'long',
                day: 'numeric',
                year: 'numeric'
               }).format(new Date(date))
      }
  }
}
</script>

<style>
#app {
  width: 80%;
  max-width: 800px;
  margin: 0px auto;
  font-family: 'Gotham-Medium',Helvetica,Arial,Lucida,sans-serif;
}

.flip-container {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  column-gap: 2em;
  
}

.fliphtml5 {
    width: 100%; 
    max-width: 800px; 
}

.fliphtml5 iframe {
    width: 100%;
    height: 700px;
}

.date{
  margin-top: 0px;
  font-size: small;
  color: gray;
}

.flipbookName{
  margin-bottom: .5em;
}

.button {
  background: linear-gradient(302deg, rgba(121,9,9,1) 0%, rgba(255,0,0,1) 100%);
  color: white;
  font-weight: 700;
  font-size: 2em;
  width: 50px;
  height: 50px;
  display: flex;
  border-radius: 10em;
  justify-content: center;
  align-items: center;
}

.button:hover {
  cursor: pointer;
}

.releases {
  /* background-color: maroon; */
  position: relative;
}
.releases-container {
  display: flex;
  padding: 1em;
  gap: 1em;
  max-width: 100%;
  flex-wrap: wrap;
}

/* .releases-container::-webkit-scrollbar{
  display: none;
} */

.preview {
  max-width: 150px;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
  display: flex;
  flex-direction: column;
  gap: .5em;
  margin-bottom: 1em;
}

.preview-name{
  text-overflow: ellipsis;
  overflow: hidden;
  margin: 0px;
  font-weight: bold;
}

.date-released{
  margin: 0px;
  font-size: small;
}

.previewImg {
  width: 150px;
  height: 200px;
  /* border: .5em maroon solid; */
}

.previewImg-selected{
  width: 150px;
  height: 200px;
  border: .5em rgba(255, 0, 0, 0.338) solid;
}

.preview:hover, .preview-selected:hover{
  cursor: pointer;
}

.left-gradient {
  width: 100px;
  height: 100%;
  background: linear-gradient(to right, #800000 0%, #ff99cc00 100%);
  position: absolute;
  left: 0px;
  top: 0px;
}

.right-gradient {
  width: 100px;
  height: 100%;
  background: linear-gradient(to left, #800000 0%, #ff99cc00 100%);
  position: absolute;
  right: 0px;
  top: 0px;
}

</style>
