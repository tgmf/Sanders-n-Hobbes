<template>
  <div class="flex flex-col space-y-2">
    <label for="topic" class="text-sm font-medium">Topic:</label>
    <textarea
      id="topic"
      v-model="localTopic"
      class="border-2 border-gray-300 rounded px-3 py-2"
      @input="updateTopic()"
    />
  </div>
</template>

<script>
export default {
  props: {
    topic: {
      type: String,
      default: ''
    }
  },
  data () {
    return {
      localTopic: this.topic
    }
  },
  watch: {
    topic () {
      this.localTopic = this.topic
    }
  },
  methods: {
    updateTopic () {
      this.$emit('update-topic', this.localTopic)
    }
  }
}
</script>
