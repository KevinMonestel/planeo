<template>
  <div>
    <input
      v-model="email"
      type="email"
    />

    <input
      v-model="password"
      type="password"
    />

    <button @click="signInWithOtp">
      Sign In with E-Mail
    </button>
  </div>
</template>

<script setup lang="ts">
const user = useSupabaseUser()
const supabase = useSupabaseClient()

const email = ref('')
const password = ref('')

const signInWithOtp = async () => {
  const { error } = await supabase.auth.signInWithPassword({
    email: email.value,
    password: password.value
  })
  if (error) console.log(error)
}
</script>