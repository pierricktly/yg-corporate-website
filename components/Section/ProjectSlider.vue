<template>
  <section>
    <Swiper
      ref="swiperVideo"
      id="swiperVideo"
      class="swiper-video"
      :loop="true"
      :allow-touch-move="false"
      @swiper="onSwiper"
      @slideChange="onSlideChange"
    >
      <SwiperSlide v-for="(artist, index) in artists" :key="artist.videoId">
        <SliderVideo
          :name="artist.name"
          :socials="artist.socials"
          :idVideo="artist.videoId"
          :playVideo="activeSlider === index"
          :initialTime="getInitialTime(artist.videoId)"
          @videoTimeUpdate="setVideoTime($event, artist.videoId)"
        />
      </SwiperSlide>
      <button
        id="prev"
        @click="goPrev"
        class="absolute left-0 top-[50%] z-10 flex items-center gap-2 pr-5 font-bold text-white mix-blend-difference"
      >
        <div class="h-0.5 w-10 rounded-full bg-white" />
        <p>Prev</p>
      </button>
      <button
        id="next"
        @click="goNext"
        class="absolute right-0 top-[50%] z-10 flex items-center gap-2 pl-5 font-bold text-white mix-blend-difference"
      >
        <p>Next</p>
        <div class="h-0.5 w-10 rounded-full bg-white" />
      </button>
    </Swiper>
  </section>
</template>

<script setup>
import { Swiper, SwiperSlide } from 'swiper/vue'
import { artists } from '~/constant/data'

const swiperVideo = ref(null)
const activeSlider = ref(0)
const videoTimes = ref({})
const swiperObject = ref(null)

const getInitialTime = (videoId) => {
  return videoTimes.value[videoId] || {}
}

const setVideoTime = (time, videoId) => {
  videoTimes.value[videoId] = time
}

const onSlideChange = (event) => {
  activeSlider.value = event.realIndex
}

const onSwiper = (swiper) => {
  swiperObject.value = swiper
}

const goNext = () => {
  if (swiperVideo.value) {
    swiperObject.value.slideNext()
  }
}

const goPrev = () => {
  if (swiperVideo.value) {
    swiperObject.value.slidePrev()
  }
}
</script>
