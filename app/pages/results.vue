<template>
  <div class="max-w-3xl mx-auto px-4 py-8">
    <h1 class="text-3xl font-bold mb-2 text-center font-serif">
      {{ $t('result') }}
    </h1>
    <p class="mb-6 text-base leading-relaxed text-center text-gray-600">
      {{ $t('results_desc') }}
    </p>

    <div ref="resultsContainer" class="mb-6">
      <ResultsImage v-if="results" :axes="results" />
    </div>

    <div class="flex flex-wrap gap-3 justify-center mb-4">
      <UButton
        :label="linkCopied ? $t('link_copied') : $t('copy_link')"
        color="neutral"
        variant="outline"
        @click="copyLink"
      />
      <UButton
        :label="$t('download')"
        color="neutral"
        variant="outline"
        @click="downloadImage"
      />
      <UButton
        :label="$t('twitter_share')"
        color="neutral"
        variant="outline"
        :to="twitterShareUrl"
        target="_blank"
        external
      />
      <UButton
        :label="$t('reddit_share')"
        color="neutral"
        variant="outline"
        :to="redditShareUrl"
        target="_blank"
        external
      />
    </div>

    <p
      v-if="linkCopied && currentUrl"
      class="text-center text-xs text-gray-500 mb-6 break-all"
    >
      {{ currentUrl }}
    </p>

    <div v-if="bonusAxes.length > 0" class="mt-8 mb-8">
      <h2 class="text-xl font-bold mb-4 font-serif">
        {{ $t('bonus_chars') }}
      </h2>
      <div class="flex flex-col gap-5">
        <div
          v-for="bonus in bonusAxes"
          :key="bonus.key"
          class="flex items-start gap-4"
        >
          <img
            :src="`/images/${bonus.key}_small.png`"
            :alt="bonus.key"
            class="w-16 h-16 shrink-0"
          />
          <div>
            <h3 class="font-bold text-base">
              {{ $t(`${bonus.i18nKey}`) }}
            </h3>
            <p class="text-sm leading-relaxed text-gray-600">
              {{ $t(`${bonus.i18nKey}_desc`) }}
            </p>
          </div>
        </div>
      </div>
    </div>

    <div class="flex justify-center mt-8">
      <NuxtLinkLocale to="/">
        <UButton :label="$t('restart_test')" color="primary" size="xl" />
      </NuxtLinkLocale>
    </div>
  </div>
</template>

<script setup lang="ts">
const route = useRoute()
const localePath = useLocalePath()
const { decodeResultsStr, decodeLegacyResultsStr, encodeResultsStr } =
  useSerializer()

const results = computed<AxisValues | null>(() => {
  try {
    const ret = decodeResultsStr(route.hash.slice(1))
    if (!ret) throw new Error()
    return ret
  } catch {
    try {
      const ret = decodeLegacyResultsStr(Object.keys(route.query)[0] as string)
      if (!ret) throw new Error()
      navigateTo(
        localePath({ name: 'results', hash: `#${encodeResultsStr(ret)}` })
      )
      return null
    } catch {
      return null
    }
  }
})

const resultsContainer = ref<HTMLElement | null>(null)
const linkCopied = ref(false)
const currentUrl = ref('')

onMounted(() => {
  currentUrl.value = window.location.href
})

const twitterShareUrl = computed(() => {
  if (!currentUrl.value) return '#'
  return `https://twitter.com/intent/tweet?url=${encodeURIComponent(currentUrl.value)}`
})

const redditShareUrl = computed(() => {
  if (!currentUrl.value) return '#'
  return `https://www.reddit.com/submit?url=${encodeURIComponent(currentUrl.value)}`
})

const copyLink = async () => {
  if (!currentUrl.value) return
  try {
    await navigator.clipboard.writeText(currentUrl.value)
    linkCopied.value = true
    setTimeout(() => {
      linkCopied.value = false
    }, 3000)
  } catch {
    // fallback: select text
  }
}

const downloadImage = async () => {
  const container = resultsContainer.value
  if (!container) return
  const svg = container.querySelector('svg')
  const canvas = container.querySelector('canvas')
  if (!svg) return

  const canvasDataUrl = canvas ? canvas.toDataURL('image/png') : null

  const svgClone = svg.cloneNode(true) as SVGSVGElement
  if (canvasDataUrl) {
    const foreignObject = svgClone.querySelector('foreignObject')
    if (foreignObject) {
      const img = document.createElementNS('http://www.w3.org/2000/svg', 'image')
      img.setAttribute('href', canvasDataUrl)
      img.setAttribute('x', foreignObject.getAttribute('x') ?? '0')
      img.setAttribute('y', foreignObject.getAttribute('y') ?? '0')
      img.setAttribute('width', foreignObject.getAttribute('width') ?? '512')
      img.setAttribute('height', foreignObject.getAttribute('height') ?? '256')
      const transform = foreignObject.getAttribute('transform')
      if (transform) img.setAttribute('transform', transform)
      foreignObject.replaceWith(img)
    }
  }

  const svgData = new XMLSerializer().serializeToString(svgClone)
  const svgBlob = new Blob([svgData], { type: 'image/svg+xml;charset=utf-8' })
  const svgUrl = URL.createObjectURL(svgBlob)

  const svgWidth = svg.viewBox.baseVal.width || 800
  const svgHeight =
    svg.viewBox.baseVal.height || parseInt(svg.getAttribute('height') ?? '600')

  const offCanvas = document.createElement('canvas')
  offCanvas.width = svgWidth
  offCanvas.height = svgHeight
  const ctx = offCanvas.getContext('2d')
  if (!ctx) {
    URL.revokeObjectURL(svgUrl)
    return
  }

  const imgEl = new Image()
  imgEl.onload = () => {
    ctx.fillStyle = '#f3f4f6'
    ctx.fillRect(0, 0, svgWidth, svgHeight)
    ctx.drawImage(imgEl, 0, 0)
    URL.revokeObjectURL(svgUrl)
    const link = document.createElement('a')
    link.download = 'politiscales_result.png'
    link.href = offCanvas.toDataURL('image/png')
    link.click()
  }
  imgEl.src = svgUrl
}

const bonusAxisMap: Record<UnpairedAxesKey, string> = {
  anarchism: 'anarchist',
  pragmatism: 'pragmatist',
  feminism: 'feminist',
  complotism: 'conspiracist',
  veganism: 'vegan',
  monarchism: 'monarchist',
  religion: 'missionary'
}

const bonusAxes = computed(() => {
  if (!results.value) return []
  return (Object.keys(badgeThreshold) as UnpairedAxesKey[])
    .filter((key) => {
      const value = results.value![key]
      return value !== null && value !== undefined && value >= (badgeThreshold[key] ?? 1)
    })
    .map((key) => ({
      key,
      i18nKey: bonusAxisMap[key] ?? key
    }))
})
</script>
