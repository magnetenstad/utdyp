<template>
  <div class="row spaced">
    <h2>Quiz: {{ quiz.subject }}</h2>
    <div v-if="showEdit">
      <button @click="router.push({ name: 'edit' })">Edit</button>
    </div>
  </div>

  <p v-if="quiz.questions.length == 0">
    Go <router-link to="/edit">add some questions</router-link>!
  </p>

  <div v-for="(q, i) in quiz.questions">
    <hr />
    <QuestionForm
      :question="q"
      :index="i + 1"
      @submit="submit"
      @requestHint="onRequestHint"
      :submitDisabled="submitDisabled"
    ></QuestionForm>
  </div>
  <div>
    <p>Score: {{ getScorePercentage(quiz) }}</p>
  </div>
</template>

<script lang="ts" setup>
import QuestionForm from './QuestionForm.vue'
import { ref } from 'vue'
import {
  Question,
  Quiz,
  getScorePercentage,
  requestGrade,
  requestHint,
} from '@/stores/quiz'
import { router } from '@/router'
import { RouterLink } from 'vue-router'

const props = withDefaults(
  defineProps<{
    quiz: Quiz
    showEdit: boolean
  }>(),
  {
    showEdit: true,
  },
)

const submitDisabled = ref(false)

const submit = async (question: Question) => {
  submitDisabled.value = true
  question.comment = 'Loading..'
  await requestGrade(props.quiz.subject, question)
  submitDisabled.value = false
}

const onRequestHint = async (question: Question) => {
  submitDisabled.value = true
  question.hint = 'Loading..'
  await requestHint(props.quiz.subject, question)
  submitDisabled.value = false
}
</script>
