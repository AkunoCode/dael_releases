<template>
  <div class="flip-container" v-if="isShowFlipbook">
    <div class="fliphtml5">
      <button @click="toggleShowFlipbook">X</button>
      <iframe :src="flipbookData[book_index]['url']" frameborder="0" scrolling="no"></iframe>
    </div>
  </div>
  <h3>Newspaper</h3>
  <div class="releases">
      <div class="releases-container">
        <ReleasesPreview 
        v-for="(flipbook, index) in flipbookData.filter(flipbook => flipbook.type === 'Newspaper')" 
        :key="index"
        :imgURL = "flipbook['img']"
        :name = "flipbook['name']"
        :dateReleased = "flipbook['date_released']"
        @click="() => {chooseIndex(index); if (!isShowFlipbook) toggleShowFlipbook()}"
        />
      </div>
  </div>
  <h3>Magazine</h3>
  <div class="releases">
      <div class="releases-container">
        <ReleasesPreview 
        v-for="(flipbook, index) in flipbookData.filter(flipbook => flipbook.type === 'Magazine')" 
        :key="index"
        :imgURL = "flipbook['img']"
        :name = "flipbook['name']"
        :dateReleased = "flipbook['date_released']"
        @click="() => {chooseIndex(index); if (!isShowFlipbook) toggleShowFlipbook()}"
        />
      </div>
  </div>
  <h3>Andamyo</h3>
  <div class="releases">
      <div class="releases-container">
        <ReleasesPreview 
        v-for="(flipbook, index) in flipbookData.filter(flipbook => flipbook.type === 'Andamyo')" 
        :key="index"
        :imgURL = "flipbook['img']"
        :name = "flipbook['name']"
        :dateReleased = "flipbook['date_released']"
        @click="() => {chooseIndex(index); if (!isShowFlipbook) toggleShowFlipbook()}"
        />
      </div>
  </div>
</template>

<script>
import FlipbookData from './FlipbookData.json'
import ReleasesPreview from '@/components/ReleasesPreview.vue'

export default {
  name: 'App',
  components: {
    ReleasesPreview
  },
  data(){
    return {
      flipbookData: [],
      book_index: 0,
      isShowFlipbook : false,
    }
  },
  created(){
    this.fetchData();
  },
  methods:{
      toggleShowFlipbook(){
          this.isShowFlipbook = !this.isShowFlipbook
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
      async fetchData(){
        try {
          // const response = await fetch("https://theluzonian.press/wp-content/releases-leo/FlipbookData.json");
          // const data = await response.json();
          // this.flipbookData = data.flipbookData;

          this.flipbookData = FlipbookData.flipbookData
        } catch (err) {
          console.error('Error fetching flipbook data:' + err)
        }
      }
  }
}
</script>

<style>
#app {
  width: 100%;
  max-width: 800px;
  margin: 0px auto;
  font-family: 'Gotham-Medium', Helvetica, Arial, Lucida, sans-serif;
}

.flip-container {
  position: absolute;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  column-gap: 2em;
  width: 100%;
  height: 100vh;
  top: 0;
  left: 0;
  z-index: 1;
  background-color: rgba(0, 0, 0, 0.528);
  backdrop-filter: blur(5px);
}

.fliphtml5 {
  width: 100%; 
  max-width: 1000px; 
  max-height: 900px;
  height: 100%;
}

.fliphtml5 iframe {
    width: 100%;
    height: 100%;
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

</style>
