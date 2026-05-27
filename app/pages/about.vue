<template>
  <div class="max-w-2xl mx-auto px-4 py-10 flex flex-col gap-8">
    <div>
      <h1 class="text-4xl font-serif font-bold mb-2">{{ $t('about_title') }}</h1>
      <p class="text-gray-500 dark:text-gray-400">{{ $t('about_subtitle') }}</p>
    </div>

    <section class="flex flex-col gap-3">
      <h2 class="text-2xl font-serif font-semibold">{{ $t('about_what_title') }}</h2>
      <p>{{ $t('about_what_body') }}</p>
    </section>

    <section class="flex flex-col gap-3">
      <h2 class="text-2xl font-serif font-semibold">{{ $t('about_axes_title') }}</h2>
      <p>{{ $t('about_axes_body') }}</p>
      <div class="grid grid-cols-1 sm:grid-cols-2 gap-2 mt-2">
        <div
          v-for="pair in axesPairs"
          :key="pair.name"
          class="flex items-center gap-2 bg-gray-50 dark:bg-gray-900 rounded-lg px-3 py-2 text-sm border border-gray-200 dark:border-gray-800"
        >
          <span class="font-medium">{{ $t(`axes.${pair.left}`) }}</span>
          <span class="text-gray-400 mx-1">vs</span>
          <span class="font-medium">{{ $t(`axes.${pair.right}`) }}</span>
        </div>
      </div>
      <p class="text-sm text-gray-500 dark:text-gray-400 mt-2">{{ $t('about_axes_unpaired') }}</p>
      <div class="flex flex-wrap gap-2">
        <UBadge
          v-for="axis in unpairedAxesKeys"
          :key="axis"
          color="neutral"
          variant="subtle"
        >
          {{ $t(`axes.${axis}`) }}
        </UBadge>
      </div>
    </section>

    <section class="flex flex-col gap-3">
      <h2 class="text-2xl font-serif font-semibold">{{ $t('about_scoring_title') }}</h2>
      <p>{{ $t('about_scoring_body') }}</p>
    </section>

    <section class="flex flex-col gap-3">
      <h2 class="text-2xl font-serif font-semibold">{{ $t('about_legacy_title') }}</h2>
      <p>{{ $t('about_legacy_body') }}</p>
    </section>

    <div class="mt-4">
      <NuxtLinkLocale to="questions">
        <UButton :label="$t('start_test')" color="primary" icon="i-lucide-arrow-right" trailing />
      </NuxtLinkLocale>
    </div>
  </div>
</template>

<script setup lang="ts">
const axesPairs = pairedAxesByPair
</script>
