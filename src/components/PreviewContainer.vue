<template>
    <div>
      <Vue3Marquee
        gradient="true"
        pauseOnHover="true"
        animateOnOverflow="true"
        v-if="false"
        :direction="direction"
      >
        <slot></slot>
      </Vue3Marquee>
      <div class="releases" v-if="true">
        <div class="releases-container">
          <slot></slot>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import { Vue3Marquee } from 'vue3-marquee';
  
  export default {
    name: "PreviewContainer",
    props: {
      isAtDedicatedPage: Boolean,
      isAtGeneralPage: Boolean,
      givenDirection: String
    },
    components: {
      Vue3Marquee
    },
    computed: {
      direction() {
        return this.givenDirection === "left" ? "normal" : "reverse";
      }
    }
  };
  </script>
  