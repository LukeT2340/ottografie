<script setup lang="ts">
import { ref, onMounted } from "vue"
import { gsap } from "gsap"
import type { Ref } from "vue"

// Declare slideEls as a Ref of HTMLElement array
const slideEls: Ref<HTMLElement[]> = ref([])

onMounted(() => {
  slideEls.value.forEach((el, index) => {
    // Ensure that the element exists before applying animations
    if (el) {
      gsap.to(el, {
        scale: 1,
        duration: 2.4,
        ease: "power3.easeOut",
        delay: index * 1.4 + 0.4,
      })

      gsap.to(el, {
        clipPath: "polygon(0% 0%, 100% 0%, 100% 100%, 0% 100%)",
        translateY: 0, // Restore translateY to 0 after animation
        duration: 0.6,
        ease: "power2.easeOut",
        delay: index * 1.4 + 0.4,
        onComplete: () => {
          gsap.to(el, {
            overflowY: "auto",
            duration: 0.1,
            delay: 0,
            ease: "power3.easeOut",
          })
        },
      })
    }
  })
})
</script>

<template>
  <div class="relative z-20">
    <!-- First image section -->
    <div
      class="absolute inset-0 w-screen h-screen overflow-hidden object-cover"
    >
      <img
        src="/assets/images/background-5-86ad29d1.jpg"
        :ref="el => slideEls[0] = el as HTMLElement"
        class="w-screen h-screen scale-125"
        style="
          clip-path: polygon(54% 77%, 54% 77%, 54% 100%, 54% 100%);
          transform: translateY(40%);
        "
      />
    </div>

    <!-- Second image section -->
    <div
      class="absolute inset-0 w-screen h-screen overflow-hidden object-cover"
    >
      <img
        src="/assets/images/background-4-7e0501e3.jpg"
        :ref="el => slideEls[1] = el as HTMLElement"
        class="w-screen h-screen scale-125"
        style="
          clip-path: polygon(54% 77%, 54% 77%, 54% 100%, 54% 100%);
          transform: translateY(40%);
        "
      />
    </div>

    <!-- Third image section -->
    <div
      class="absolute inset-0 w-screen h-screen overflow-hidden object-cover"
    >
      <img
        src="/assets/images/background-8370ffe4.jpg"
        :ref="el => slideEls[2] = el as HTMLElement"
        class="w-screen h-screen scale-125"
        style="
          clip-path: polygon(54% 77%, 54% 77%, 54% 100%, 54% 100%);
          transform: translateY(40%);
        "
      />
    </div>

    <!-- Rest of scrollable page here -->
    <div
      :ref="el => slideEls[3] = el as HTMLElement"
      class="absolute inset-0 w-screen h-screen object-cover bg-black"
      style="
        clip-path: polygon(54% 77%, 54% 77%, 54% 100%, 54% 100%);
        overflow-y: hidden;
      "
    >
      <scrollable />
    </div>
  </div>
</template>
