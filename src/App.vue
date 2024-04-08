<template>
  <div class="flip-container" v-if="isShowFlipbook" @click.self="toggleShowFlipbook" >
    <div class="fliphtml5">
      <!-- dont show x if mobile -->
      <button id="close-flipbook" @click="toggleShowFlipbook" v-if="!isMobile">X</button>
      <iframe :src="flipbookData[this.type][this.book_index]['url']" frameborder="0" scrolling="no" allowfullscreen webkitallowfullscreen></iframe>
    </div>
  </div>
  <div v-if="isAtReleases || isAtNewspaper">
    <h2>Newspaper</h2>
    <hr class="divider">
    <PreviewContainer :isAtDedicatedPage="isAtNewspaper" :isAtGeneralPage="isAtReleases" givenDirection="left">
      <ReleasesPreview
      v-for="(flipbook, index) in this.newspapers"
      :key="index"
      :imgURL = "flipbook['img']"
      :name = "flipbook['name']"
      :dateReleased = "flipbook['date_released']"
      @click="() => {chooseIndex(index, 'newspaper'); if (!isShowFlipbook) toggleShowFlipbook()}"
      />
    </PreviewContainer>
  </div>
  <div v-if="isAtReleases || isAtMagazine">
    <h2>Magazine</h2>
    <hr class="divider">
    <PreviewContainer :isAtDedicatedPage="isAtMagazine" :isAtGeneralPage="isAtReleases" givenDirection="right">
      <ReleasesPreview
      v-for="(flipbook, index) in this.magazines"
      :key="index"
      :imgURL = "flipbook['img']"
      :name = "flipbook['name']"
      :dateReleased = "flipbook['date_released']"
      @click="() => {chooseIndex(index, 'magazine'); if (!isShowFlipbook) toggleShowFlipbook()}"
      />
    </PreviewContainer>
  </div>
  <div v-if="isAtReleases || isAtAndamyo">
    <h2>Andamyo</h2>
    <hr class="divider">
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
import FlipbookData from '@/FlipbookData.json' // For development
import ReleasesPreview from '@/components/ReleasesPreview.vue'
import PreviewContainer from '@/components/PreviewContainer.vue'

export default {
  name: 'App',
  components: {
    ReleasesPreview, PreviewContainer
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
      isAtNewspaper: false,
      isMobile: false
    }
  },
  created(){
    this.fetchData();
  },
  mounted(){
    this.checkPath()
    this.checkMobile()
    window.addEventListener('popstate', this.onMobileBack)
  },
  beforeUnmount(){
    window.removeEventListener('popstate', this.onMobileBack)
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
          if (window.location.hostname === 'localhost' || window.location.hostname === '127.0.0.1') {
            this.flipbookData = FlipbookData.flipbookData; // For development
          } else {
            const response = await fetch("https://theluzonian.press/wp-content/releases-leo/FlipbookData.json");
            const data = await response.json();
            this.flipbookData = data.flipbookData;
          }

          // this.flipbookData = FlipbookData.flipbookData // For development

          // For production
          
          //const response = await fetch("https://theluzonian.press/wp-content/releases-leo/FlipbookData.json");
          //const data = await response.json();
          //this.flipbookData = data.flipbookData;
          
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
      },
      checkMobile(){
        if (window.innerWidth <= 400){
          this.isMobile = true
        } else {
          this.isMobile = false
        }
      },
      onMobileBack(){
        if (this.isShowFlipbook){
          this.toggleShowFlipbook()
        } else {
          window.history.back()
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

.divider {
  border: 1px solid #800000;
  margin-bottom: 20px;
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
  max-height: 700px;
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

.vue3-marquee.horizontal{
  gap: 1em;
  z-index: 0;
}

.vue3-marquee.horizontal>.marquee{
  gap: 1em;
  margin: 0.5em 0px;
}

.vue3-marquee.horizontal>.overlay:before, .vue3-marquee.horizontal>.overlay:after{
  pointer-events: none;
}

@media screen and (max-width: 400px){
  .releases-container{
    justify-content: center;
    align-items: center;
    gap: 1em;
  }

  .fliphtml5 {
    max-width: 100%;
    max-height: 70%;
  }

  .vue3-marquee.horizontal>.overlay:before, .vue3-marquee.horizontal>.overlay:after{
    display: none;
  }
  
}

</style>
