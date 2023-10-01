<template>
  <div class="h-full flex justify-center items-center">
    <UButton color="gray" variant="solid" label="Create or join to a room" @click="createModalIsOpen = !createModalIsOpen"/>

    <UModal v-model="createModalIsOpen">
      <UCard :ui="{ divide: 'divide-y divide-gray-100 dark:divide-gray-800' }">
        <template #header>
          Create or join
        </template>
        <LayoutFormCreateRoom />
      </UCard>
    </UModal>
  </div>
</template>

<script setup lang="ts">
const createModalIsOpen = ref(false)
</script>