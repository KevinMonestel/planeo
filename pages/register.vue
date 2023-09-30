<template>
  <div class="h-full flex flex-col justify-center items-center">
    <UCard>

    <div class="mb-3">
      <h1>Sign Up</h1>
    </div>
    <UForm :validate="validate" :state="state" @submit="submit">
      <UFormGroup label="Email" name="email" required>
        <UInput v-model="state.email" type="email" placeholder="email" icon="i-heroicons-envelope" />
      </UFormGroup>
      <UFormGroup class="mt-2" label="Password" name="password" required>
        <UInput v-model="state.password" type="password" placeholder="password" icon="i-heroicons-key" />
      </UFormGroup>
      <UFormGroup class="mt-2">
        <UButton type="submit" block>
          Submit
        </UButton>
      </UFormGroup>
    </UForm>
    <div class="text-center">
        <span class="text-sm">- or -</span>
        <div class="mt-3">
          <UButton variant="link">
            <NuxtLink to="/login">Sign In</NuxtLink>
          </UButton>
        </div>
      </div>
    </UCard>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import type { FormError, FormSubmitEvent } from '@nuxt/ui/dist/runtime/types'

const supabase = useSupabaseClient()

const state = ref({
  email: undefined,
  password: undefined
})

const validate = (state: any): FormError[] => {
  const errors = []
  if (!state.email) errors.push({ path: 'email', message: 'Required' })
  if (!state.password) errors.push({ path: 'password', message: 'Required' })
  return errors
}

async function submit(event: FormSubmitEvent<any>) {
  const { error } = await supabase.auth.signUp({
    email: event.data.email,
    password: event.data.password
  })
  if (error) console.log(error)
}
</script>