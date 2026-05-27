<template>
  <div class="max-w-2xl mx-auto px-4 py-12 flex flex-col items-center gap-8 text-center">
    <div class="flex flex-col items-center gap-3">
      <h1 class="text-5xl font-serif font-bold">Politiscales</h1>
      <p class="text-lg text-gray-500 dark:text-gray-400">
        {{ $t('tagline') }}
      </p>
    </div>

    <div class="flex flex-col gap-3 text-left max-w-lg w-full bg-gray-50 dark:bg-gray-900 rounded-xl p-6 border border-gray-200 dark:border-gray-800">
      <div class="flex items-start gap-3">
        <UIcon name="i-lucide-list-checks" class="mt-1 shrink-0 text-primary" size="20" />
        <span>{{ $t('info_questions') }}</span>
      </div>
      <div class="flex items-start gap-3">
        <UIcon name="i-lucide-flag" class="mt-1 shrink-0 text-primary" size="20" />
        <span>{{ $t('info_flag') }}</span>
      </div>
      <div class="flex items-start gap-3">
        <UIcon name="i-lucide-share-2" class="mt-1 shrink-0 text-primary" size="20" />
        <span>{{ $t('info_share') }}</span>
      </div>
    </div>

    <NuxtLinkLocale to="questions">
      <UButton :label="$t('start_test')" color="primary" size="xl" icon="i-lucide-arrow-right" trailing />
    </NuxtLinkLocale>

    <NuxtLinkLocale to="about" class="text-sm text-gray-400 hover:text-gray-600 dark:hover:text-gray-200 underline underline-offset-2">
      {{ $t('learn_more') }}
    </NuxtLinkLocale>
  </div>
</template>
