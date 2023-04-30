<template>
  <div class="flex flex-col space-y-2">
    <label for="topic" class="text-sm font-medium">Topic:</label>
    <textarea
      id="topic"
      v-model="topic"
      class="border-2 border-gray-300 rounded px-3 py-2"
      @input="saveTopic()"
    />
  </div>
</template>

<script>
export default {
  data () {
    return {
      topic: ''
    }
  },
  methods: {
    saveTopic () {
      this.$nextTick(() => {
        this.$emit('update-topic', this.topic)
      })
    }
  }
}
</script>
