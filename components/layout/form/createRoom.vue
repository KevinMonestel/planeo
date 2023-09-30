<template>
  <UForm :validate="validate" :state="state" @submit="submit">
    <UFormGroup label="Name" name="roomName">
      <UInput v-model="state.roomName" />
    </UFormGroup>
    <UButton type="submit" class="mt-2">
      Submit
    </UButton>
  </UForm>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import type { FormError, FormSubmitEvent } from '@nuxt/ui/dist/runtime/types'

const router = useRouter()

const state = ref({
  roomName: undefined
})

const validate = (state: any): FormError[] => {
  const errors = []
  if (!state.roomName) errors.push({ path: 'roomName', message: 'Required' })
  return errors
}

async function submit(event: FormSubmitEvent<any>) {
  router.push({ path: `"/room` });
  console.log(event.data)
}

</script>