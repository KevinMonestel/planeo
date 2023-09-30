<template>
  <div class="h-full flex justify-center items-center">
    <UButton color="primary" variant="solid" label="Join" class="mr-2" @click="joinModalIsOpen = !joinModalIsOpen"/>
    <UButton color="gray" variant="solid" label="Create Room" @click="createModalIsOpen = !createModalIsOpen"/>

    <UModal v-model="joinModalIsOpen">
      <UCard :ui="{ divide: 'divide-y divide-gray-100 dark:divide-gray-800' }">
        <template #header>
          Join
        </template>
        <!-- Content -->
      </UCard>
    </UModal>

    <UModal v-model="createModalIsOpen">
      <UCard :ui="{ divide: 'divide-y divide-gray-100 dark:divide-gray-800' }">
        <template #header>
          Create new room
        </template>
        <LayoutFormCreateRoom />
      </UCard>
    </UModal>
  </div>
</template>

<script setup lang="ts">
const joinModalIsOpen = ref(false)
const createModalIsOpen = ref(false)
</script>