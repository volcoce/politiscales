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
  // First calculate raw scores as before
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

  // Normalize paired axes so neither side of a pair exceeds 1.0
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

  // Convert to 0-1 fractions (null when no questions were answered for that axis)
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
  <i18n-t
    keypath="question_x_of_n"
    scope="global"
    tag="span"
    class="text-xl my-4 font-serif"
  >
    <template #x>
      <span>{{ questionsState.currentQuestionIndex + 1 }}</span>
    </template>
    <template #n>
      {{ questionsIds.length }}
    </template>
  </i18n-t>
  <h2 class="text-2xl my-4 min-h-[3lh]">
    {{ currentQuestion }}
  </h2>
  <div class="flex flex-col gap-4 mt-16 max-w-[256px] m-auto">
    <UButton color="neutral" size="xl" @click="nextQuestion(1)">
      {{ $t('strong_agree') }}
    </UButton>
    <UButton color="neutral" size="xl" @click="nextQuestion(2 / 3)">
      {{ $t('agree') }}
    </UButton>
    <UButton color="neutral" size="xl" @click="nextQuestion(0)">
      {{ $t('neutral') }}
    </UButton>
    <UButton color="neutral" size="xl" @click="nextQuestion(-2 / 3)">
      {{ $t('disagree') }}
    </UButton>
    <UButton color="neutral" size="xl" @click="nextQuestion(-1)">
      {{ $t('strong_disagree') }}
    </UButton>

    <UButton
      v-if="questionsState.currentQuestionIndex > 0"
      color="neutral"
      size="xl"
      @click="prevQuestion"
    >
      {{ $t('prev_question') }}
    </UButton>
    <UButton
      v-if="questionsState.currentQuestionIndex === 0"
      color="neutral"
      size="xl"
      to="/"
    >
      {{ $t('back_home') }}
    </UButton>
  </div>
</template>

<style></style>
