<template>
    <div class="preview">
        <img class="previewImg" :src='getImgUrl(imgURL)' :alt="name" />
        <div class="text-container">
          <p class="preview-name">{{ name }}</p>
          <p class="date-released">{{ formatDate(dateReleased) }}</p>
        </div>
    </div>
</template>
<script>
export default {
    name: "ReleasesPreview",
    props: {
        imgURL: String,
        name: String,
        dateReleased: String,
    },
    methods: {
        getImgUrl(img){
          if (window.location.hostname === 'localhost' || window.location.hostname === '127.0.0.1') {
            return require('@/assets/'.concat(img, '.png'))
          } else {
            return 'https://theluzonian.press/wp-content/releases-leo/img/' + img + ".png" // For production
          }
            //return 'https://theluzonian.press/wp-content/releases-leo/img/' + img + ".png" // For production
            //return require('@/assets/'.concat(img, '.png')) // For development
        },
        formatDate(date){
            return Intl.DateTimeFormat('en-US', {
                month: 'long',
                day: 'numeric',
                year: 'numeric'
            }).format(new Date(date))
        },
    }
}
</script>
<style scoped>
.preview {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  overflow: hidden;
  max-width: 10em;
  gap: .5em;
  border: 1px solid rgba(128, 128, 128, 0.493);
  border-radius: .2em;
  aspect-ratio: 5/7;
}

.text-container{
  position: absolute;
  bottom: -100%;
  display: flex;
  justify-content: flex-end;
  text-align: center;
  flex-direction: column;
  gap: .5em;
  padding-bottom: 1em;
  height: 50%;
  width: 100%;
  background: linear-gradient(0deg, rgb(139, 1, 1) 30%, rgba(139, 1, 1, 0) 100%);
  color: white;
}

.preview-name{
  text-overflow: ellipsis;
  overflow: hidden;
  margin: 0px;
  font-weight: bold;
}

.date-released{
  display: none;
  margin: 0px;
  font-size: small;
}

.previewImg {
  width: 100%;
  aspect-ratio: 5/7;
  /* border: .5em maroon solid; */
}

.preview:hover, .preview-selected:hover{
  cursor: pointer;
}

.preview:hover .text-container{
  bottom: 0;
  transition: bottom .5s;
}

.preview:hover{
  /* Transition to add box shadow and maroon border */
  box-shadow: 0px 0px 10px 0px maroon;
  border: 1px maroon solid;
  transition: box-shadow .5s, border .5s;
}

@media screen and (max-width: 400px){
  .preview{
    max-width: 9em;
    aspect-ratio: 5/7;
  }
}
</style>