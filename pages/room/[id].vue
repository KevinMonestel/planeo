<template>
  <div></div>
</template>

<script setup lang="ts">
const spClient = useSupabaseClient()
const spUser = useSupabaseUser()
const route = useRoute()
const toast = useToast()

onMounted(() => {
  let roomName = route.params.id

  const channel = spClient.channel(roomName)

  channel.subscribe((status) => {
    if (status === 'SUBSCRIBED') {
      toast.add({ title: 'Woho!', description: `Suscribed to ${roomName}`, color: 'primary', icon: 'i-heroicons-information-circle' })

      let user = spUser.value?.email
      let message = `${user} joined`

      channel.send({
        type: 'broadcast',
        event: 'user-suscribed',
        payload: { message: message },
      })
    }
  })

  channel.on(
    'broadcast',
    { event: 'user-suscribed' },
    (payload) => toast.add({ title: 'Woho!', description: `${payload.payload.message}`, color: 'primary', icon: 'i-heroicons-information-circle' })
  )
})
</script>