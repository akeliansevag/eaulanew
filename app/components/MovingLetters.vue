<template>
  <ClientOnly>
    <component :is="tag" ref="textRef" :class="['ml12', customClass]">
      <slot />
    </component>
  </ClientOnly>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const props = defineProps({
  tag: { type: String, default: 'h1' }, // any tag
  customClass: { type: String, default: '' },
  loop: { type: Boolean, default: true }
})

const textRef = ref(null)

onMounted(async () => {
  if (!process.client) return

  // ✅ dynamic import of animejs v4
  const animeModule = await import('animejs')
  const anime = animeModule.default // must access .default

  const textWrapper = textRef.value
  if (!textWrapper) return

  // wrap each letter in a span
  textWrapper.innerHTML = textWrapper.textContent.replace(/\S/g, "<span class='letter'>$&</span>")

  // run animation
  anime.timeline({ loop: props.loop })
    .add({
      targets: '.ml12 .letter',
      translateX: [40, 0],
      translateZ: 0,
      opacity: [0, 1],
      easing: "easeOutExpo",
      duration: 1200,
      delay: (el, i) => 500 + 30 * i
    })
    .add({
      targets: '.ml12 .letter',
      translateX: [0, -30],
      opacity: [1, 0],
      easing: "easeInExpo",
      duration: 1100,
      delay: (el, i) => 100 + 30 * i
    })
})
</script>

<style scoped>
.ml12 {
  font-weight: 200;
  font-size: 3rem;
  text-transform: uppercase;
  letter-spacing: 0.2em;
  display: inline-block;
}

.ml12 .letter {
  display: inline-block;
  line-height: 1em;
}
</style>
