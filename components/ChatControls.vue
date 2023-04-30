<template>
  <div class="flex gap-4">
    <button
      class="bg-blue-500 text-white py-2 px-4 rounded hover:bg-blue-600"
      @click="toggleConversation"
    >
      {{ localConversationActive ? "Stop" : "Go" }}
    </button>
    <button
      class="bg-yellow-500 text-white py-2 px-4 rounded hover:bg-yellow-600"
      @click="regenerateResponse"
    >
      Regenerate
    </button>
    <button
      class="bg-blue-500 text-white py-2 px-4 rounded hover:bg-blue-600"
      @click="toggleControl"
    >
      {{ aiModerator ? "Take control" : "Give up control" }}
    </button>
    <button
      class="bg-red-500 text-white py-2 px-4 rounded ml-auto hover:bg-red-600"
      @click="reset"
    >
      Reset
    </button>
  </div>
</template>

<script>
export default {
  props: {
    aiModerator: {
      type: Boolean,
      default: true
    },
    conversationActive: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      localConversationActive: this.conversationActive
    }
  },
  methods: {
    toggleConversation () {
      this.localConversationActive = !this.localConversationActive
      this.$emit('toggle-conversation', this.localConversationActive)
    },
    regenerateResponse () {
      this.$emit('regenerate-response')
    },
    toggleControl () {
      this.$emit('toggle-control')
    },
    reset () {
      this.$emit('reset')
    }
  }
}
</script>
