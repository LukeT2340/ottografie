<script setup lang="ts">
import { ref, onMounted, onUnmounted, nextTick } from "vue"
import { gsap } from "gsap"
import type { Ref } from "vue"

// Define your refs and state
const number = ref(0)
const numberEls: Ref<HTMLElement[]> = ref([])
const headingEls: Ref<HTMLElement[]> = ref([])
const labelsEls: Ref<HTMLElement[]> = ref([])
const hasAnimated = ref(false)
const isLoaded = ref(false)
const timer = ref<NodeJS.Timer | null>(null)

gsap.to(labelsEls.value, {
  onStart: () => {
    nextTick(() => {
      gsap.to(labelsEls.value, {
        y: 0,
        opacity: 1,
        duration: 0.5,
        ease: "power2.in",
        stagger: 0.1,
      })
    })
  },
})

gsap.to(numberEls.value, {
  onStart: () => {
    nextTick(() => {
      gsap.to(numberEls.value, {
        y: 0,
        opacity: 1,
        duration: 0.5,
        ease: "power2.out",
        stagger: 0.1,
        delay: 0.3,
      })
    })
  },
})

// Function to increment digits and animate
const incrementDigits = async () => {
  const randomIncrement = Math.floor(Math.random() * 80) + 1
  const newValue = Math.min(number.value + randomIncrement, 99)
  if (hasAnimated.value) return

  if (numberEls.value.length) {
    // Animate out the old digits
    await new Promise<void>((resolve) => {
      gsap.to(numberEls.value, {
        y: -190,
        duration: 0.5,
        ease: "power2.in",
        stagger: 0.1,
        onComplete: resolve,
      })
    })

    // Update the number after the animation
    number.value = newValue
    await nextTick()

    if (number.value !== 99) {
      // Animate in the new digits
      gsap.fromTo(
        numberEls.value,
        { y: 190 },
        {
          y: 0,
          duration: 0.5,
          ease: "power2.out",
          stagger: 0.1,
        }
      )
    } else {
      // Final Animations
      hasAnimated.value = true
      cleanupAnimations()
    }
  }
}

const cleanupAnimations = async () => {
  await new Promise<void>((resolve) => {
    gsap.fromTo(
      numberEls.value,
      { y: 190 },
      {
        y: 0,
        duration: 0.5,
        ease: "power2.out",
        stagger: 0.1,
        onComplete: resolve,
      }
    )
  })

  gsap.to(labelsEls.value, {
    y: -30,
    duration: 0.5,
    ease: "power2.in",
    stagger: 0.1,
    delay: 0,
  })

  await new Promise<void>((resolve) => {
    gsap.to(numberEls.value, {
      y: -190,
      duration: 0.5,
      ease: "power2.in",
      stagger: 0.1,
      onComplete: resolve,
      delay: 0,
    })
  })

  gsap.fromTo(
    headingEls.value,
    { y: 80, opacity: 0 },
    {
      y: "0",
      opacity: 1,
      duration: 0.5,
      delay: 0.4,
      ease: "power2.out",
      stagger: 0.03,
      onComplete: () => {
        isLoaded.value = true
      },
    }
  )

  gsap.fromTo(
    headingEls.value,
    { y: 0 },
    {
      y: "-70vh",
      duration: 1,
      ease: "power1.in",
      stagger: 0,
      delay: 1.3,
    }
  )
}

onMounted(() => {
  // Start the interval to increment digits
  timer.value = setInterval(() => {
    incrementDigits()

    if (number.value === 99) {
      clearInterval(timer.value)
    }
  }, 1200)
})

// Clear the timer on component unmount
onUnmounted(() => {
  if (timer.value) {
    clearInterval(timer.value)
  }
})
</script>

<template>
  <div ref="smoothScrollWrapper">
    <div class="bg-black w-screen h-screen relative">
      <div
        class="absolute left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 overflow-y-hidden text-white text-[150px]"
      >
        <div class="relative">
          <span
            v-for="(digit, index) in number
              .toString()
              .padStart(2, '0')
              .split('')"
            :key="index"
            :ref="el => numberEls[index] = el as HTMLElement"
            class="inline-block mr-0 relative opacity-0 translate-y-[120px]"
          >
            {{ digit }}
          </span>
        </div>
      </div>
      <div
        class="absolute left-1/2 top-1/2 -translate-y-1/2 -translate-x-1/2 text-white text-[150px]"
      >
        <span
          v-for="(letter, index) in 'Ottografie'.split('')"
          :key="index"
          :ref="el => headingEls[index] = el as HTMLElement"
          class="inline-block translate-y-[190px] opacity-0"
        >
          {{ letter }}
        </span>
      </div>
      <div
        class="absolute top-1/2 flex w-full justify-between items-center overflow-hidden uppercase text-white text-xl px-[6vw]"
      >
        <span
          v-for="(label, index) in [
            'Campaigns',
            'Editorial',
            'Celebrities',
            'Beauty',
          ]"
          :key="index"
          :ref="el => labelsEls[index] = el as HTMLElement"
          class="opacity-100 translate-[30px] w-[150px]"
        >
          {{ label }}
        </span>
      </div>
      <div v-if="isLoaded">
        <slides />
      </div>
    </div>
  </div>
</template>
