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

    <button @click="register">
      Sign In with E-Mail
    </button>
  </div>
</template>

<script setup lang="ts">
const supabase = useSupabaseClient()
const email = ref('')
const password = ref('')

const register = async () => {
  const { error } = await supabase.auth.signUp({
    email: email.value,
    password: password.value
  })
  if (error) console.log(error)
}
</script>