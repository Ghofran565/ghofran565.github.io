<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'

const props = defineProps({
  speed: { type: Number, default: 0.06 },
  amplitude: { type: Number, default: 1 },
  isCode: { type: Boolean, default: false },
  isSong: { type: Boolean, default: false },
  isGame: { type: Boolean, default: false } // kept for compatibility
})

const phase = ref(0)
let rafId

// Dynamic text based on activity
const displayText = computed(() => {
  if (props.isCode && props.isSong) {
    return 'vibing'
  } else if (props.isCode) {
    return 'coding'
  } else if (props.isSong) {
    return 'doing sth'
  } else {
    return 'doing sth'
  }
})

// Dynamic gradient based on activity
const gradientStyle = computed(() => {
  let stops

  if (props.isCode && props.isSong) {
    // coding + song → vibing → aqua blue green
    stops = '#00ffff, #0088ff, #00ff88'
  } else if (props.isCode) {
    // coding → blue aqua
    stops = '#0088ff, #00ffff'
  } else if (props.isSong) {
    // any other + song → doing sth → aqua green lime
    stops = '#00ffff, #00ff88, #aaff00'
  } else {
    // other activities (no song) → doing sth → green lime
    stops = '#00ff88, #aaff00'
  }

  return {
    backgroundImage: `linear-gradient(90deg, ${stops})`,
    backgroundSize: '200% 100%',
    backgroundClip: 'text',
    WebkitBackgroundClip: 'text',
    color: 'transparent'
  }
})

const chars = computed(() => displayText.value.split(''))

const charStyle = (idx) => {
  const wave = Math.sin(phase.value - idx * 0.5) * props.amplitude
  const intensity = Math.max(0, wave)

  return {
    display: 'inline-block',
    transform: `translateY(${-intensity * 8}px) scale(${1 + intensity * 0.35})`,
    opacity: 0.6 + intensity * 0.4,
    willChange: 'transform, opacity'
  }
}

const animate = () => {
  phase.value += props.speed
  rafId = requestAnimationFrame(animate)
}

onMounted(() => animate())
onBeforeUnmount(() => cancelAnimationFrame(rafId))
</script>

<template>
  <p class="text-center text-2xl font-semibold whitespace-nowrap overflow-hidden">
    <span
      v-for="(char, idx) in chars"
      :key="idx"
      class="inline-block"
      :style="charStyle(idx)"
    >
      <span
        :style="gradientStyle"
        class="inline-block"
      >
        {{ char === ' ' ? '\u00A0' : char }}
      </span>
    </span>
  </p>
</template>

<style scoped>
p {
  animation: shimmer 4s linear infinite;
}

@keyframes shimmer {
  0% {
    background-position: 0% 50%;
  }
  100% {
    background-position: 200% 50%;
  }
}
</style>