<template lang="html">
  <form @submit.prevent="createIssue">
    <card
      title="Créer une bounty"
      :blue="true"
    >
      <label class="input-username">Titre</label>
      <input
        type="text"
        class="input-username"
        v-model="title"
      />
      <label class="input-username">Description</label>
      <textarea class="input-username" v-model="description" />
      <label class="input-username">Lien</label>
      <input type="text" class="input-username" v-model="link" />
      <label class="input-username">Récompence</label>
      <input type="number" class="input-username" v-model="reward"/>
      <button type="submit" class="input-username">Créer</button>
    </card>
  </form>
</template>

<script lang="ts">
import { computed, defineComponent } from 'vue'
import Card from '@/components/Card.vue'
import { useStore } from 'vuex'

export default defineComponent({
  name: 'CreateIssue',
  components: { Card },
  setup() {
    const store = useStore()
    const address = computed(() => store.state.account.address)
    const contract = computed(() => store.state.contract)
    return { address, contract }
  },
  data() {
    const id = this.$route.query.id
    const ownerAddress = this.$route.query.ownerAddress
    const title = ''
    const description = ''
    const link = ''
    const reward = 0
    return { id, ownerAddress, title, description, link, reward }
  },
  methods: {
    async createIssue() {
      const { contract, id, ownerAddress, title, description, link, reward } =
        this
      await contract.methods
        .createAnIssue(ownerAddress, id, title, description, link, reward)
        .send()
      this.$router.push({
        name: 'FullRecapProject',
        query: {
          id: this.id,
          ownerAddress: this.ownerAddress,
        }
      })
    },
  },
})
</script>

<style lang="css">
.input-username {
  background: transparent;
  border: none;
  padding: 12px;
  outline: none;
  width: 100%;
  color: white;
  font-family: inherit;
  font-size: 1.3rem;
}
</style>
