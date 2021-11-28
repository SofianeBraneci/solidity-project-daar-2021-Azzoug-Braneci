<template lang="html">
  <card
    :title="`${this.project.name}`"
    :blue ="true"
  >
    <div style="padding: 12px">
      <p>
        <b>Créateur: </b>
        {{
          this.project.ownedByUser
            ? this.project.owner.username
            : this.project.owner.name
        }}
        &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;
        <b>Adresse: </b>
        {{ this.project.ownerAddress }}
         &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;
           Balance: {{ this.project.balance }} Tokens
      </p>
      <div>
        <b>Contributeurs: </b>
        <p
          v-for="contributor in this.project.contributors"
          v-bind:key="contributor.address"
          style="padding-left: 10px"
        >
          {{ contributor.account.username }} &nbsp;
          {{ contributor.address }}
         
        </p>
      </div>
      <b v-if="issues.length > 0">Bounties: </b>
      <div
        v-for="issue in this.issues"
        v-bind:key="issue.id"
      >
        <resume-issue :issue="issue" :project-id="project.id"></resume-issue>
      </div>
      <p>
        <a href="#" style="color: white" @click="createIssue">
          Créer une Bounty
        </a>
      </p>
    

    </div>
  </card>
</template>

<script lang="ts">
import { computed, defineComponent } from 'vue'
import Card from '@/components/Card.vue'
import { useStore } from 'vuex'
import ResumeIssue from "@/components/ResumeIssue.vue";

export default defineComponent({
  name: 'FullRecapProject',
  components: { ResumeIssue, Card },
  methods: {
    async updateProjectBalance() {
      const project = await this.contract.methods
        .getProjectByIdAndAddress(this.ownerAddress, this.id)
        .call()
      this.project.balance = project.balance
    },
    async createIssue() {
      this.$router.push({
        name: 'CreateIssue',
        query: {
          id: this.project?.id,
          ownerAddress: this.project?.ownerAddress,
        }
      })
    },
  },
  setup() {
    const store = useStore()
    const address = computed(() => store.state.account.address)
    const balance = computed(() => store.state.account.balance)
    const contract = computed(() => store.state.contract)
    return { address, contract, balance }
  },
  data() {
    const showModal = false
    const id = this.$route.query.id
    const ownerAddress = this.$route.query.ownerAddress
    const project: any = {
      id: '',
      name: '',
      owner: {
        name: '',
        username: '',
        balance: 0,
      },
      ownerAddress: 'project.owner',
      ownedByUser: true,
      balance: 0,
      contributors: [],
    }
    //const project = JSON.parse(this.$route.params.project.toString())
    const issues: never[] = []
    return { showModal, id, ownerAddress, project, issues }
  },
  async mounted() {
    const { contract, id, ownerAddress } = this
    this.issues = await contract.methods.getIssuesByProjectId(id).call()
    const project = await contract.methods
      .getProjectByIdAndAddress(ownerAddress, id)
      .call()
    let name = project.name
    let owner = null
    if (project.ownedByUser) {
      owner = await contract.methods.getUserByAddress(project.owner).call()
    } else {
      owner = await contract.methods
        .getEnterpriseByAddress(project.owner)
        .call()
    }
    let balance = project.balance
    const contributorsAddress = project.contributorsAddress
    let contributors = []
    for (const contributorsAddressKey of contributorsAddress) {
      const contributor = await contract.methods
        .getUserByAddress(contributorsAddressKey)
        .call()
      contributors.push({
        address: contributorsAddressKey,
        account: {
          username: contributor.username,
          balance: contributor.balance,
          registered: contributor.registered,
        },
      })
    }
    this.project = {
      id: project.id,
      name: name,
      owner: {
        name: owner.name || undefined,
        username: owner.username || undefined,
        balance: owner.balance,
      },
      ownerAddress: project.owner,
      ownedByUser: project.ownedByUser,
      balance: balance,
      contributors: contributors,
    }
    console.log(this.project)
  },
})
</script>
