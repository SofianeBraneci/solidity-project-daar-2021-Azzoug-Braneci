<template lang="html">
  <div style="padding: 12px">
    <p><b>Nom du projet: </b>{{ project.name }}</p>
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
        v-for="contributor in project.contributors"
        v-bind:key="contributor.address"
        style="padding-left: 10px"
      >
        {{ contributor.account.username }} &nbsp; Adresse:
        {{ contributor.address }}
      </p>
    </div>
    <a href="#" style="color: white" @click="gotoFullRecap">
      Détails
    </a>
  </div>
</template>

<script lang="ts">
import { computed, defineComponent } from 'vue'
import { useStore } from 'vuex'

export default defineComponent({
  name: 'ResumeProject',
  props: { project: Object },
  methods: {
    gotoFullRecap() {
      this.$router.push({
        name: 'FullRecapProject',
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
})
</script>
