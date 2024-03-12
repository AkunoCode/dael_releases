<template>
    <div class="preview">
        <img class="previewImg" :src='getImgUrl(imgURL)' :alt="name" />
        <p class="preview-name">{{ name }}</p>
        <p class="date-released">{{ formatDate(dateReleased) }}</p>
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
            // return 'https://theluzonian.press/wp-content/releases-leo/img/' + img + ".png"
            return require('@/assets/'.concat(img, '.png'))
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
  max-width: 150px;
  /* overflow: hidden; */
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
  border: 1px solid grey;
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
</style>