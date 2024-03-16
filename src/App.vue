<template>
  <div class="flip-container" v-if="isShowFlipbook">
    <div class="fliphtml5">
      <button @click="toggleShowFlipbook" id="close-flipbook">X</button>
      <iframe :src="flipbookData[this.type][this.book_index]['url']" frameborder="0" scrolling="no"></iframe>
    </div>
  </div>
  <div v-if="isAtReleases || isAtNewspaper">
    <h2>Newspaper</h2>
    <div class="releases">
        <div class="releases-container">
          <ReleasesPreview 
          v-for="(flipbook, index) in this.newspapers" 
          :key="index"
          :imgURL = "flipbook['img']"
          :name = "flipbook['name']"
          :dateReleased = "flipbook['date_released']"
          @click="() => {chooseIndex(index, 'newspaper'); if (!isShowFlipbook) toggleShowFlipbook()}"
          />
        </div>
    </div>
  </div>
  <div v-if="isAtReleases || isAtMagazine">
    <h2>Magazine</h2>
    <div class="releases">
        <div class="releases-container">
          <ReleasesPreview 
          v-for="(flipbook, index) in this.magazines" 
          :key="index"
          :imgURL = "flipbook['img']"
          :name = "flipbook['name']"
          :dateReleased = "flipbook['date_released']"
          @click="() => {chooseIndex(index, 'magazine'); if (!isShowFlipbook) toggleShowFlipbook()}"
          />
        </div>
    </div>
  </div>
  <div v-if="isAtReleases || isAtAndamyo">
    <h2>Andamyo</h2>
    <div class="releases">
        <div class="releases-container">
          <ReleasesPreview 
          v-for="(flipbook, index) in this.andamyos" 
          :key="index"
          :imgURL = "flipbook['img']"
          :name = "flipbook['name']"
          :dateReleased = "flipbook['date_released']"
          @click="() => {chooseIndex(index, 'andamyo'); if (!isShowFlipbook) toggleShowFlipbook()}"
          />
        </div>
    </div>
  </div>
</template>

<script>
// import FlipbookData from '@/FlipbookData.json' // For development
import ReleasesPreview from '@/components/ReleasesPreview.vue'

export default {
  name: 'App',
  components: {
    ReleasesPreview
  },
  data(){
    return {
      flipbookData: [],
      newspapers: [],
      magazines: [],
      andamyos: [],
      book_index: 0,
      type: 'newspapers',
      isShowFlipbook : false,
      isAtReleases: true,
      isAtMagazine: false,
      isAtAndamyo: false,
      isAtNewspaper: false
    }
  },
  created(){
    this.fetchData();
  },
  mounted(){
    this.checkPath()
  },
  methods:{
      toggleShowFlipbook(){
          this.isShowFlipbook = !this.isShowFlipbook
          if (this.isShowFlipbook) {
            document.body.style.overflow = 'hidden'; // Prevent scrolling
          } else {
            document.body.style.overflow = ''; // Enable scrolling
          }
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
      chooseIndex(index, type){
        this.book_index = index;
        this.type = type;
      },
      async fetchData(){
        try {
          // this.flipbookData = FlipbookData.flipbookData // For development

          // For production
          const response = await fetch("https://theluzonian.press/wp-content/releases-leo/FlipbookData.json");
          const data = await response.json();
          this.flipbookData = data.flipbookData;

          this.newspapers = this.sortByDate(this.flipbookData.newspaper)
          this.magazines = this.sortByDate(this.flipbookData.magazine)
          this.andamyos = this.sortByDate(this.flipbookData.andamyo)
        } catch (err) {
          console.error('Error fetching flipbook data:' + err)
        }
      },
      sortByDate(data) {
        return data.sort((a, b) => {
          return new Date(b.date_released) - new Date(a.date_released);
        });
      },
      checkPath(){
        let currentPath = window.location.href
        if (currentPath.includes('newspaper')){
          this.isAtNewspaper = true
          this.isAtMagazine = false
          this.isAtAndamyo = false
          this.isAtReleases = false
        } else if (currentPath.includes('magazine')){
          this.isAtNewspaper = false
          this.isAtMagazine = true
          this.isAtAndamyo = false
          this.isAtReleases = false
        } else if (currentPath.includes('andamyo')){
          this.isAtNewspaper = false
          this.isAtMagazine = false
          this.isAtAndamyo = true
          this.isAtReleases = false
        } else {
          this.isAtNewspaper = false
          this.isAtMagazine = false
          this.isAtAndamyo = false
          this.isAtReleases = true
        }
      }
    }
  }
</script>

<style>
#app {
  width: 100%;
  max-width: 900px;
  margin: 0px auto;
  font-family: 'Geologica-Black',Helvetica,Arial,Lucida,sans-serif;
}

.flip-container {
  position: fixed;
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
  /* padding: 1em; */
  gap: .8em;
  max-width: 100%;
  flex-wrap: wrap;
  margin-bottom: 3em;
}

#close-flipbook{
  background-color: maroon;
  color: white;
  border: none;
  font-weight: bold;
  padding: .5em;
}

#close-flipbook:hover{
  cursor: pointer;
}

</style>
