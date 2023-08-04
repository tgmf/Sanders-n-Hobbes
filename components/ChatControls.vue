<template>
  <div class="flex gap-4">
    <button
      class="bg-blue-500 text-white py-2 px-4 rounded hover:bg-blue-600"
      @click="toggleConversation"
    >
      {{ reset ? "Start" : conversationActive ? "Stop" : "Go" }}
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
      @click="resetConversation"
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
    },
    reset: {
      type: Boolean,
      default: true
    }
  },
  data () {
    return {
      localConversationActive: this.conversationActive
    }
  },
  watch: {
    conversationActive: {
      handler (newVal) {
        this.localConversationActive = newVal
      }
    }
  },
  methods: {
    toggleConversation () {
      // this.localConversationActive = !this.localConversationActive
      this.$emit('toggle-conversation')
    },
    regenerateResponse () {
      this.$emit('regenerate-response')
    },
    toggleControl () {
      this.$emit('toggle-control')
    },
    resetConversation () {
      this.$emit('reset-conversation')
    }
  }
}
</script>
