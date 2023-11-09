<template>
  <div
    class="relative h-screen overflow-hidden bg-cover bg-center bg-no-repeat"
    :style="`background-image:  url('https://img.youtube.com/vi/${idVideo}/maxresdefault.jpg') ;`"
  >
    <div
      class="absolute inset-0 z-20 flex w-full items-center justify-center bg-black/30"
    >
      <div class="space-y-5 text-white">
        <p class="text-center text-4xl font-bold 2xl:text-7xl">{{ name }}</p>
        <div class="flex justify-center gap-3">
          <NuxtLink :to="socials.youtube" target="_blank">
            <IconYoutube class="h-4 w-4" />
          </NuxtLink>
          <NuxtLink :to="socials.instagram" target="_blank">
            <IconInstagram class="h-4 w-4" />
          </NuxtLink>
          <NuxtLink :to="socials.twitter" target="_blank">
            <IconX class="h-4 w-4" />
          </NuxtLink>
          <NuxtLink :to="socials.weibo" target="_blank">
            <IconWeibo class="h-4 w-4" />
          </NuxtLink>
        </div>
        <!-- <button
        @click="toggleMute"
        >
        Mute/Unmute
      </button> -->
      </div>
    </div>

    <YoutubeIframe
      ref="YoutubeIframeComponent"
      :idVideo="props.idVideo"
      :initialTime="initialTime.time || 0"
      @videoLoaded="handleVideoLoaded"
      @videoTimeUpdate="updateTime"
      class="transition-all duration-1000 ease-in-out"
      :class="YoutubeIframeIsPlaying ? 'opacity-100' : 'opacity-0'"
    />
  </div>
</template>

<script lang="ts" setup>
// Définition des types pour les props
type Props = {
  idVideo: string
  playVideo?: boolean
  initialTime?: Object
  name: string
  socials: Object
}

// Utilisez defineProps pour déclarer les props et leurs types
const props = defineProps<Props>()
const emit = defineEmits(['videoTimeUpdate'])

const YoutubeIframeComponent = ref(null)
const YoutubeIframeIsPlaying = ref(false)

const callPlayPauseIframe = () => {
  if (!YoutubeIframeComponent.value) {
    console.log('Le composant YoutubeIframe n’est pas prêt.')
    return
  }

  if (YoutubeIframeIsPlaying.value) {
    YoutubeIframeComponent.value.stopVideo()
    YoutubeIframeIsPlaying.value = false
  } else {
    YoutubeIframeComponent.value.playVideo()
    // attendre 2 secondes avant de lancer
    setTimeout(() => {
      YoutubeIframeIsPlaying.value = true
    }, definedTimeToShowVideo())
  }
}

const toggleMute = () => {
  if (!YoutubeIframeComponent.value) {
    console.log('Le composant YoutubeIframe n’est pas prêt.')
    return
  }
  YoutubeIframeComponent.value?.toggleMute()
}

const handleVideoLoaded = (isLoaded: boolean) => {
  if (isLoaded && props.playVideo) {
    YoutubeIframeComponent.value?.playVideo()
    setTimeout(() => {
      YoutubeIframeIsPlaying.value = true
    }, definedTimeToShowVideo())
  }
}

const definedTimeToShowVideo = () => {
  if (props.initialTime?.time > 0) {
    return 3500
  }
  return 5000
}

const updateTime = (time: Number) => {
  emit('videoTimeUpdate', { time, idVideo: props.idVideo })
}

// Utilisez watch pour surveiller les changements de props.playVideo
watch(
  () => props.playVideo,
  (newValue, oldValue) => {
    if (newValue !== oldValue) {
      if (newValue) {
        YoutubeIframeComponent.value?.playVideo()
        setTimeout(() => {
          YoutubeIframeIsPlaying.value = true
        }, definedTimeToShowVideo())
      } else {
        YoutubeIframeComponent.value?.stopVideo()
        YoutubeIframeIsPlaying.value = false
      }
    }
  },
  { immediate: true },
)
</script>