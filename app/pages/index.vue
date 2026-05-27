<template>
  <div class="max-w-2xl mx-auto px-4 py-8">
    <div class="flex justify-center mb-8">
      <img
        src="/images/politiscales_cover.png"
        alt="PolitiScales"
        class="max-w-sm w-full"
      />
    </div>

    <h2 class="text-2xl font-bold mb-4 font-serif">
      {{ $t('home_subtitle') }}
    </h2>

    <p class="mb-6 text-base leading-relaxed">
      {{ $t('description') }}
    </p>

    <div class="flex flex-col gap-3 mb-8">
      <div class="flex items-start gap-3">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="shrink-0 mt-0.5 text-yellow-500"
          width="24"
          height="24"
          viewBox="0 0 24 24"
        >
          <path
            fill="currentColor"
            d="M8.27,3L3,8.27V15.73L8.27,21H15.73C17.5,19.24 21,15.73 21,15.73V8.27L15.73,3M9.1,5H14.9L19,9.1V14.9L14.9,19H9.1L5,14.9V9.1M11,15H13V17H11V15M11,7H13V13H11V7"
          />
        </svg>
        <p class="text-sm leading-relaxed">{{ $t('info_1') }}</p>
      </div>

      <div class="flex items-start gap-3">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="shrink-0 mt-0.5 text-blue-400"
          width="24"
          height="24"
          viewBox="0 0 24 24"
        >
          <path
            fill="currentColor"
            d="M11,9H13V7H11M12,20C7.59,20 4,16.41 4,12C4,7.59 7.59,4 12,4C16.41,4 20,7.59 20,12C20,16.41 16.41,20 12,20M12,2A10,10 0 0,0 2,12A10,10 0 0,0 12,22A10,10 0 0,0 22,12A10,10 0 0,0 12,2M11,17H13V11H11V17Z"
          />
        </svg>
        <p class="text-sm leading-relaxed">{{ $t('info_2') }}</p>
      </div>

      <div class="flex items-start gap-3">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="shrink-0 mt-0.5 text-red-500"
          width="24"
          height="24"
          viewBox="0 0 24 24"
        >
          <path
            fill="currentColor"
            d="M2.2,16.06L3.88,12L2.2,7.94L6.26,6.26L7.94,2.2L12,3.88L16.06,2.2L17.74,6.26L21.8,7.94L20.12,12L21.8,16.06L17.74,17.74L16.06,21.8L12,20.12L7.94,21.8L6.26,17.74L2.2,16.06M4.81,9L6.05,12L4.81,15L7.79,16.21L9,19.19L12,17.95L15,19.19L16.21,16.21L19.19,15L17.95,12L19.19,9L16.21,7.79L15,4.81L12,6.05L9,4.81L7.79,7.79L4.81,9M11,15H13V17H11V15M11,7H13V13H11V7"
          />
        </svg>
        <p class="text-sm leading-relaxed">{{ $t('warning') }}</p>
      </div>
    </div>

    <div class="flex justify-center">
      <NuxtLinkLocale to="questions">
        <UButton :label="$t('start_test')" color="primary" size="xl" />
      </NuxtLinkLocale>
    </div>
  </div>
</template>
