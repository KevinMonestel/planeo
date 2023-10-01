<template>
  <div class="grid grid-cols-1 md:grid-cols-3 grid-rows-1 gap-10 mt-10">
    <div>
      <UCard>
        <template #header>Users subscribed to
          <UBadge color="gray" variant="solid" :label="roomName"/>
        </template>
        <ul v-for="user in users">
          <UBadge color="green" variant="subtle" :label="user.email" class="mb-2 rounded-full" size="sm" />
        </ul>
      </UCard>
    </div>
    <div></div>
    <div>
      <div>
        <UButton label="Leave Room" class="rounded-full" variant="outline" color="red" @click="leave"/>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { Ref } from 'vue';
import { User } from '../../types/user';

const spClient = useSupabaseClient()
const spUser = useSupabaseUser()
const route = useRoute()
const router = useRouter()
const toast = useToast()
const users: Ref<User[]> = ref([])
const roomName = route.params.id.toString()
const channel = spClient.channel(roomName)

const userStatus: User = {
  email: spUser.value?.email,
  online_at: new Date().toISOString(),
  presence_ref: undefined
}

onMounted(() => {
  channel
    .on('presence', { event: 'sync' }, () => {
      const newState = channel.presenceState()
      console.log('sync', JSON.stringify(newState))

      let usersData = JSON.parse(JSON.stringify(newState));

      users.value = []

      for (const userInfo in usersData) {
        if (usersData.hasOwnProperty(userInfo)) {
          let usersArray = usersData[userInfo];

          let user: User = usersArray[0]

          users.value.push(user)
          console.log(usersArray)
        }
      }
    })
    .on('presence', { event: 'join' }, ({ key, newPresences }) => {
      console.log('join', key, JSON.stringify(newPresences))
    })
    .on('presence', { event: 'leave' }, ({ key, leftPresences }) => {
      console.log('leave', key, leftPresences)

      const jsonArray = JSON.parse(JSON.stringify(leftPresences));
      const user = jsonArray[0].email;

      toast.add({ description: user + " left" })
    })
    .subscribe(async (status) => {
      if (status !== 'SUBSCRIBED') { return }

      await channel.track(userStatus)
    })
})

const leave = () =>{
  channel.untrack()
  router.push({ path: '/' })
}
</script>