<template>
  <UApp :tooltip="{ delayDuration: 0 }">
    <NuxtLayout>
      <UMain>
        <NuxtPage />
      </UMain>
    </NuxtLayout>

    <UFooter>
      <template #center>
        <p class="text-sm text-gray-500 dark:text-gray-400 text-center leading-relaxed">
          {{ $t('footer_based_on') }}
          <a
            href="https://8values.github.io/"
            target="_blank"
            rel="noopener"
            class="underline hover:text-gray-800 dark:hover:text-gray-200"
          >8values</a>.
          {{ $t('footer_maintained_by') }}
          <a
            href="https://twitter.com/Conobipe"
            target="_blank"
            rel="noopener"
            class="underline hover:text-gray-800 dark:hover:text-gray-200"
          >Conobi</a>.
          ·
          <a
            href="https://twitter.com/rePolitiScales"
            target="_blank"
            rel="noopener"
            class="underline hover:text-gray-800 dark:hover:text-gray-200"
          >{{ $t('footer_twitter') }}</a>
          ·
          <a
            href="https://github.com/Donokami/politiscales"
            target="_blank"
            rel="noopener"
            class="underline hover:text-gray-800 dark:hover:text-gray-200"
          >{{ $t('footer_source') }}</a>
          ·
          <NuxtLinkLocale
            to="/about_data"
            class="underline hover:text-gray-800 dark:hover:text-gray-200"
          >{{ $t('footer_data_policy') }}</NuxtLinkLocale>
        </p>
      </template>
    </UFooter>
  </UApp>
</template>
