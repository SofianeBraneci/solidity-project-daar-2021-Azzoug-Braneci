<template lang="html">
  <div style="padding: 12px">
    <p>Title : {{ issue.title }}</p>
    <p><b>Description: </b>{{ issue.description }}</p>
    <p><b>Lien: </b>{{ issue.link }}</p>
    <p><b>Intiateur: </b>{{ issuerUsername }}</p>
    <p><b>Récompence: </b>{{ issue.reward }} ETHs</p>
    <p>Status: {{ issue.closed ? 'Fermé' : 'Ouvert' }}</p>

  </div>
</template>

<script lang="ts">
import { computed, defineComponent } from 'vue'
import { useStore } from 'vuex'
import FixIssueModal from '@/components/FixIssueModal.vue'

export default defineComponent({
  name: 'ResumeIssue',
  components: { },
  props: { issue: Object, projectId: String },
  data() {
    const issuerUsername = ''
    const fixerUsername = ''
    const showModal = false
    return { issuerUsername, fixerUsername, showModal }
  },
  setup() {
    const store = useStore()
    const address = computed(() => store.state.account.address)
    const balance = computed(() => store.state.account.balance)
    const contract = computed(() => store.state.contract)
    return { address, contract, balance }
  },
  async mounted() {
    const issuerAddress = this.issue?.issuer
    const issuerAccount = await this.contract.methods.getUserByAddress(issuerAddress).call()
    this.issuerUsername = issuerAccount.username
    if (this.issue?.closed) {
      const fixerAddress = this.issue?.fixer
      const fixerAccount = await this.contract.methods.getUserByAddress(fixerAddress).call()
      this.fixerUsername = fixerAccount.username
    }
  },
})
</script>
