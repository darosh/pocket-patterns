<script>
// Based on https://github.com/sveltejs/svelte/blob/e8b7cd7889e93efc7814a18249830ca596797f96/site/src/components/IntersectionObserver.svelte

import { onMount } from 'svelte'
import { createEventDispatcher } from 'svelte'

const dispatch = createEventDispatcher()

export let once = false
export let top = 0
export let bottom = 0
export let left = 0
export let right = 0

let intersecting = false
let container

onMount(() => {
  if (typeof IntersectionObserver !== 'undefined') {
    const rootMargin = `${bottom}px ${left}px ${top}px ${right}px`

    const observer = new IntersectionObserver(entries => {
      const previousIntersecting = intersecting

      intersecting = entries[0].isIntersecting

      if (intersecting && once) {
        observer.unobserve(container)
      }

      if (previousIntersecting !== intersecting) {
        dispatch('intersecting', intersecting)
      }
    }, { rootMargin })

    observer.observe(container)

    return () => observer.unobserve(container)
  }

  function handler () {
    const bcr = container.getBoundingClientRect()

    const previousIntersecting = intersecting

    intersecting = (
      (bcr.bottom + bottom) > 0 &&
      (bcr.right + right) > 0 &&
      (bcr.top - top) < window.innerHeight &&
      (bcr.left - left) < window.innerWidth
    )

    if (intersecting && once) {
      window.removeEventListener('scroll', handler)
    }

    if (previousIntersecting !== intersecting) {
      dispatch('intersecting', intersecting)
    }
  }

  window.addEventListener('scroll', handler, {passive: true})
  return () => window.removeEventListener('scroll', handler)
})
</script>

<div bind:this={container}>
  <slot {intersecting}></slot>
</div>
