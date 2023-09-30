
<template>
  <header>
    <div class="bg-secondary">
      <UContainer>
        <div class="flex justify-between items-center h-[80px]">
          <NuxtLink class="flex-1 flex items-center" to="/">
            <div class="flex mr-1 text-4xl">
              <UIcon name="i-heroicons-clock"/>
            </div>
            <div>
              <h1><span class="text-primary">PLAN</span>eo</h1>
            </div>
          </NuxtLink>
          <div class="flex-1 flex items-center justify-end">
            <UiColorPicker />
            <div v-if="user">
              <UDropdown :items="items" :popper="{ placement: 'bottom-start' }">
                <UButton color="white" :label="user.email" trailing-icon="i-heroicons-chevron-down-20-solid" />
              </UDropdown>
            </div>
          </div>
        </div>
      </UContainer>
    </div>
  </header>
</template>

<script setup lang="ts">
const user = useSupabaseUser()
const spClient = useSupabaseClient()
const router = useRouter()

const items =[
  [{
      label: 'Sign Out',
      click(){
        spClient.auth.signOut()
        user.value = null
        router.push('/login')
      }
  }]
]
</script>