<template>
  <div class="flex flex-col space-y-4">
    <h3 class="text-xl font-semibold">
      {{ agent.name || "New Debater" }}
    </h3>
    <div class="flex flex-col space-y-2">
      <label
        :for="'name-' + agent.id"
        class="text-sm font-medium"
      >
        Name:
      </label>
      <input
        :id="'name-' + agent.id"
        v-model="localAgent.name"
        type="text"
        class="border-2 border-gray-300 rounded px-3 py-2"
        @input="updateAgent()"
      >
    </div>
    <div class="flex flex-col space-y-2">
      <label
        :for="'personality-' + agent.id"
        class="text-sm font-medium"
      >
        Personality:
      </label>
      <textarea
        :id="'personality-' + agent.id"
        v-model="localAgent.personality"
        class="border-2 border-gray-300 rounded px-3 py-2"
        @input="updateAgent()"
      />
    </div>
  </div>
</template>

<script>
export default {
  props: {
    agent: {
      type: Object,
      required: true
    }
  },
  data () {
    return {
      localAgent: { ...this.agent }
    }
  },
  watch: {
    agent: {
      handler (newVal) {
        this.localAgent = { ...newVal }
      },
      deep: true
    }
  },
  methods: {
    updateAgent () {
      this.$emit('update-agent', this.localAgent)
    }
  }
}
</script>
