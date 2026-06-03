<script lang="ts" setup>
const { t } = useI18n()
const { encodeResultsStr } = useSerializer()
const questionsState = useQuestionsState()
const localePath = useLocalePath()

const currentQuestion = computed(() =>
  t(
    `questions.${questionsIds.value[questionsState.value.currentQuestionIndex]}`
  )
)
const currentQuestionId = computed(() => {
  return questionsIds.value[questionsState.value.currentQuestionIndex]
})

const questionsIds = computed(() => {
  return Object.keys(questionsWeights)
})

interface Score {
  val: number
  sum: number
}

const quizResults = computed<AxisValues>(() => {
  const scores = axesKeys.reduce(
    (acc, axis) => {
      acc[axis] = { val: 0, sum: 0 }
      return acc
    },
    {} as Record<string, Score>
  )

  Object.entries(questionsState.value.answers).forEach(
    ([questionId, answerValue]) => {
      if (answerValue > 0) {
        questionsWeights[questionId]?.valuesYes.forEach((a) => {
          ;(scores[a.axis] as Score).val += answerValue * a.value
          ;(scores[a.axis] as Score).sum += Math.max(a.value, 0)
        })
      } else {
        questionsWeights[questionId]?.valuesNo.forEach((a) => {
          ;(scores[a.axis] as Score).val -= answerValue * a.value
          ;(scores[a.axis] as Score).sum += Math.max(a.value, 0)
        })
      }
    }
  )

  const pairGroups: { [key: string]: string[] } = {}
  axesKeys.forEach((axis) => {
    const axe = axes[axis]
    if ('pair' in axe) {
      if (!pairGroups[axe.pair]) {
        pairGroups[axe.pair] = []
      }
      pairGroups[axe.pair]!.push(axis)
    }
  })

  Object.values(pairGroups).forEach((pair) => {
    const [axis1, axis2] = pair as [string, string]
    const value1 = scores[axis1]!.sum > 0 ? scores[axis1]!.val / scores[axis1]!.sum : 0
    const value2 = scores[axis2]!.sum > 0 ? scores[axis2]!.val / scores[axis2]!.sum : 0

    if (value1 + value2 > 1) {
      const ratio = 1 / (value1 + value2)
      scores[axis1]!.val *= ratio
      scores[axis2]!.val *= ratio
    }
  })

  return Object.entries(scores).reduce((acc, [axis, score]) => {
    acc[axis as AxisKey] = score.sum > 0 ? score.val / score.sum : null
    return acc
  }, {} as AxisValues)
})

const prevQuestion = () => {
  if (questionsState.value.currentQuestionIndex > 0) {
    questionsState.value.currentQuestionIndex--
  }
}

const nextQuestion = (mult: number) => {
  if (!currentQuestionId.value) {
    return
  }
  questionsState.value.answers[currentQuestionId.value] = mult
  if (
    questionsState.value.currentQuestionIndex ===
    questionsIds.value.length - 1
  ) {
    navigateTo(
      localePath({
        name: 'results',
        hash: `#${encodeResultsStr(quizResults.value)}`
      })
    )
    questionsState.value.currentQuestionIndex = 0
    questionsState.value.answers = {}
  } else {
    questionsState.value.currentQuestionIndex++
  }
}
</script>

<template>
  <div class="max-w-2xl mx-auto px-4 py-8">
    <h2 class="text-xl font-serif mb-6 text-center text-gray-500">
      <i18n-t keypath="question_x_of_n" scope="global">
        <template #x>{{ questionsState.currentQuestionIndex + 1 }}</template>
        <template #n>{{ questionsIds.length }}</template>
      </i18n-t>
    </h2>

    <div class="border border-gray-200 rounded-lg p-6 mb-8 min-h-[6rem] flex items-center justify-center bg-white dark:bg-gray-900 dark:border-gray-700">
      <p class="text-lg leading-relaxed text-center">{{ currentQuestion }}</p>
    </div>

    <div class="flex flex-col gap-3">
      <UButton
        color="success"
        size="xl"
        class="w-full justify-center"
        @click="nextQuestion(1)"
      >
        {{ $t('strong_agree') }}
      </UButton>
      <UButton
        color="success"
        variant="soft"
        size="xl"
        class="w-full justify-center"
        @click="nextQuestion(2 / 3)"
      >
        {{ $t('agree') }}
      </UButton>
      <UButton
        color="neutral"
        variant="soft"
        size="xl"
        class="w-full justify-center"
        @click="nextQuestion(0)"
      >
        {{ $t('neutral') }}
      </UButton>
      <UButton
        color="warning"
        variant="soft"
        size="xl"
        class="w-full justify-center"
        @click="nextQuestion(-2 / 3)"
      >
        {{ $t('disagree') }}
      </UButton>
      <UButton
        color="error"
        size="xl"
        class="w-full justify-center"
        @click="nextQuestion(-1)"
      >
        {{ $t('strong_disagree') }}
      </UButton>

      <div class="mt-2 flex justify-center">
        <UButton
          v-if="questionsState.currentQuestionIndex > 0"
          color="neutral"
          variant="ghost"
          @click="prevQuestion"
        >
          ← {{ $t('prev_question') }}
        </UButton>
        <NuxtLinkLocale v-else to="/">
          <UButton color="neutral" variant="ghost">
            ← {{ $t('back_home') }}
          </UButton>
        </NuxtLinkLocale>
      </div>
    </div>
  </div>
</template>
