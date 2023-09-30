<template>
  <LayoutAppContainer>
    <LayoutHeader />
    <LayoutPageContainer class="h-[calc(100vh-80px)]">
      <NuxtPage />
    </LayoutPageContainer>
  </LayoutAppContainer>
</template>

<script setup lang="ts" >
const user = useSupabaseUser()
const router = useRouter()

watchEffect(() => {
  if (user.value) {
    router.push('/')
  } else {
    router.push('/login')
  }
})
</script>
