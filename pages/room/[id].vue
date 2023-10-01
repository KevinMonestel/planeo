<template>
  <div class="grid grid-cols-2 grid-rows-2 gap-10">
    <div>
      <UCard>
        <template #header>Users subscribed to
          <UBadge color="gray" variant="solid" :label="roomName" />
        </template>
        <ul v-for="user in users">
          <UBadge color="green" variant="subtle" :label="user.email" class="mb-2 rounded-full" size="sm" />
        </ul>
      </UCard>
    </div>
    <div>
      <UButton label="Leave Room" class="rounded-full" variant="outline" color="red" @click="leave" />
    </div>
    <div class="col-span-2">
      <div class="grid grid-cols-5 grid-rows-3 gap-4">
        <div class="card" @click="vote('0')">0</div>
        <div class="card" @click="vote('1/2')">1/2</div>
        <div class="col-start-1 row-start-2 card" @click="vote('1')">1</div>
        <div class="col-start-3 row-start-1 card" @click="vote('2')">2</div>
        <div class="col-start-4 row-start-1 card" @click="vote('3')">3</div>
        <div class="col-start-5 row-start-1 card" @click="vote('5')">5</div>
        <div class="card" @click="vote('1')">8</div>
        <div class="card" @click="vote('13')">13</div>
        <div class="card" @click="vote('20')">20</div>
        <div class="card" @click="vote('40')">40</div>
        <div class="col-start-2 card" @click="vote('100')">100</div>
        <div class="col-start-3 card" @click="vote('?')">?</div>
        <div class="col-start-4 card" @click="vote('☕')">☕</div>
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

      let usersData = JSON.parse(JSON.stringify(newState));

      users.value = []

      for (const userInfo in usersData) {
        if (usersData.hasOwnProperty(userInfo)) {
          let usersArray = usersData[userInfo];

          let user: User = usersArray[0]

          users.value.push(user)
        }
      }
    })
    .on('presence', { event: 'join' }, ({ key, newPresences }) => {
      //console.log('join', key, JSON.stringify(newPresences))
    })
    .on('presence', { event: 'leave' }, ({ key, leftPresences }) => {
      console.log('leave', key, leftPresences)

      const jsonArray = JSON.parse(JSON.stringify(leftPresences));
      const user = jsonArray[0].email;

      toast.add({ description: user + " left" })
    })
    .on('broadcast', { event: 'vote' }, (payload) => {
      toast.add({ description: payload.payload.user.email + "  voted " + payload.payload.vote })
      console.log('vote', payload)
    })
    .subscribe(async (status) => {
      if (status !== 'SUBSCRIBED') { return }

      await channel.track(userStatus)
    })
})

const leave = () => {
  channel.untrack()
  router.push({ path: '/' })
}

const vote = (vote: string) => {
  channel.send({
    type: 'broadcast',
    event: 'vote',
    payload: { user: userStatus, vote: vote },
  })
}
</script>

<style scoped>
.card{
  @apply text-center px-2 py-2 rounded-full border cursor-pointer hover:bg-primary transition-all;
}
</style>