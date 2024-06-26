<template>
  <div class="flip-container" v-if="isShowFlipbook" @click.self="toggleShowFlipbook" >
    <div class="fliphtml5">
      <!-- dont show x if mobile -->
      <button id="close-flipbook" @click="toggleShowFlipbook" v-if="!isMobile">X</button>
      <iframe :src="flipbookData[this.type][this.book_index]['url']" frameborder="0" scrolling="no" allowfullscreen webkitallowfullscreen></iframe>
    </div>
  </div>
  <div v-if="(isAtReleases || isAtNewsletter) && (hasNewsletter || isAtNewsletter)">
    <h2><a href="https://theluzonian.press/index.php/category/releases/newsletter/">Newsletter</a></h2>
    <hr class="divider">
    <PreviewContainer :isAtDedicatedPage="isAtNewsletter" :isAtGeneralPage="isAtReleases" givenDirection="left">
      <ReleasesPreview
      v-for="(flipbook, index) in this.newsletters"
      :key="index"
      :imgURL = "flipbook['img']"
      :name = "flipbook['name']"
      :dateReleased = "flipbook['date_released']"
      @click="() => {chooseIndex(index, 'newsletter'); if (!isShowFlipbook) toggleShowFlipbook()}"
      />
    </PreviewContainer>
  </div>
  <div v-if="(isAtReleases || isAtTabloid) && (hasTabloid || isAtTabloid)">
    <h2><a href="https://theluzonian.press/index.php/category/releases/tabloid/">Tabloid</a></h2>
    <hr class="divider">
    <PreviewContainer :isAtDedicatedPage="isAtNewsletter" :isAtGeneralPage="isAtReleases" givenDirection="left">
      <ReleasesPreview
      v-for="(flipbook, index) in this.tabloids"
      :key="index"
      :imgURL = "flipbook['img']"
      :name = "flipbook['name']"
      :dateReleased = "flipbook['date_released']"
      @click="() => {chooseIndex(index, 'tabloid'); if (!isShowFlipbook) toggleShowFlipbook()}"
      />
    </PreviewContainer>
  </div>
  <div v-if="(isAtReleases || isAtBroadsheet) && (hasBroadsheet || isAtBroadsheet)">
    <h2><a href="https://theluzonian.press/index.php/category/releases/broadsheet/">Broadsheet</a></h2>
    <hr class="divider">
    <PreviewContainer :isAtDedicatedPage="isAtNewsletter" :isAtGeneralPage="isAtReleases" givenDirection="left">
      <ReleasesPreview
      v-for="(flipbook, index) in this.broadsheets"
      :key="index"
      :imgURL = "flipbook['img']"
      :name = "flipbook['name']"
      :dateReleased = "flipbook['date_released']"
      @click="() => {chooseIndex(index, 'broadsheet'); if (!isShowFlipbook) toggleShowFlipbook()}"
      />
    </PreviewContainer>
  </div>
  <div v-if="(isAtReleases || isAtMagazine) && (hasMagazine || isAtMagazine)">
    <h2><a href="https://theluzonian.press/index.php/category/releases/magazine/">Magazine</a></h2>
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
  <div v-if="(isAtReleases || isAtAndamyo) && (hasAndamyo || isAtAndamyo)">
    <h2><a href="https://theluzonian.press/index.php/category/releases/andamyo/">Andamyo</a></h2>
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
import $ from 'jquery'

export default {
  name: 'App',
  components: {
    ReleasesPreview, PreviewContainer
  },
  data(){
    return {
      flipbookData: [],
      newsletters: [],
      broadsheets: [],
      tabloids: [],
      magazines: [],
      andamyos: [],
      book_index: 0,
      type: 'newspapers',
      isShowFlipbook : false,
      isAtReleases: true,
      isAtMagazine: false,
      isAtAndamyo: false,
      isAtNewsletter: false,
      isAtTabloid: false,
      isAtBroadsheet: false,
      isMobile: false,
      header: null
    }
  },
  computed:{
    hasNewsletter(){
      return this.newsletters.length > 0
    },
    hasTabloid(){
      return this.tabloids.length > 0
    },
    hasBroadsheet(){
      return this.broadsheets.length > 0
    },
    hasMagazine(){
      return this.magazines.length > 0
    },
    hasAndamyo(){
      return this.andamyos.length > 0
    }
  },
  created(){
    this.fetchData();
  },
  mounted(){
    this.checkPath()
    this.checkMobile()
    window.addEventListener('popstate', this.onMobileBack)
    this.header = $('#main-header');
  },
  beforeUnmount(){
    window.removeEventListener('popstate', this.onMobileBack)
  },
  methods:{
      toggleShowFlipbook(){
          this.isShowFlipbook = !this.isShowFlipbook
          if (this.isShowFlipbook) {
            document.body.style.overflow = 'hidden'; // Prevent scrolling
            this.header.css('display', 'none');
          } else {
            document.body.style.overflow = ''; // Enable scrolling
            this.header.css('display', 'block');
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

          this.newsletters = this.sortByDate(this.flipbookData.newsletter)
          this.tabloids = this.sortByDate(this.flipbookData.tabloid)
          this.broadsheets = this.sortByDate(this.flipbookData.broadsheet)
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
        if (currentPath.includes('newsletter')){
          this.isAtNewsletter = true
          this.isAtMagazine = false
          this.isAtAndamyo = false
          this.isAtTabloid = false
          this.isAtBroadsheet = false
          this.isAtReleases = false
        } else if (currentPath.includes('magazine')){
          this.isAtNewsletter = false
          this.isAtMagazine = true
          this.isAtAndamyo = false
          this.isAtTabloid = false
          this.isAtBroadsheet = false
          this.isAtReleases = false
        } else if (currentPath.includes('andamyo')){
          this.isAtNewsletter = false
          this.isAtMagazine = false
          this.isAtAndamyo = true
          this.isAtTabloid = false
          this.isAtBroadsheet = false
          this.isAtReleases = false
        } else if (currentPath.includes('tabloid')){
          this.isAtNewsletter = false
          this.isAtMagazine = false
          this.isAtAndamyo = false
          this.isAtTabloid = true
          this.isAtBroadsheet = false
          this.isAtReleases = false
        } else if (currentPath.includes('broadsheet')){
          this.isAtNewsletter = false
          this.isAtMagazine = false
          this.isAtAndamyo = false
          this.isAtTabloid = false
          this.isAtBroadsheet = true
          this.isAtReleases = false
        } else {
          this.isAtNewsletter = false
          this.isAtMagazine = false
          this.isAtAndamyo = false
          this.isAtTabloid = false
          this.isAtBroadsheet = false
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
          this.header.css('display', 'none');
        } else {
          window.history.back()
          this.header.css('display', 'block');
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

a {
  text-decoration: none;
  color: rgb(31, 30, 30);
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
