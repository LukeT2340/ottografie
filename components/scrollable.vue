<script setup lang="ts">
import { onMounted, ref } from "vue"
import { gsap } from "gsap"

onMounted(() => {
  const textEl = document.querySelector("h1") as HTMLElement
  const letters = Array.from(textEl?.textContent || "").map((char) => {
    return `<span class="opacity-0 translate-y-[50px]">${char}</span>`
  })
  textEl!.innerHTML = letters.join("")

  const spans = textEl!.querySelectorAll("span")

  const observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          gsap.to(spans, {
            opacity: 1,
            y: 0,
            duration: 1,
            ease: "power3.easeOut",
            stagger: 0.1,
          })
          observer.unobserve(entry.target)
        }
      })
    },
    { threshold: 0.5 }
  )
  observer.observe(textEl)
})
</script>

<template>
  <div class="h-[400vw] bg-[#f5f0eb] relative">
    <h1
      class="text-[150px] fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2"
    >
      Beauty
    </h1>
  </div>
</template>
