<template>
  <div
    v-show="visible"
    class="fixed inset-0 bg-white z-50 flex items-center justify-center"
  >
    <div class="w-full max-w-md mx-auto p-6">
      <h2 class="text-3xl font-bold mb-4">
        Enter OpenAI's API Key
      </h2>
      <p class="md:max-w-3/4 lg:max-w:1/4 mb-4">
        We are NOT saving or sending your key anywhere. We use it only in this session. It stays only in your browser
      </p>
      <div class="flex flex-col space-y-4">
        <label for="api-key" class="font-medium">
          API Key:
        </label>
        <input
          id="api-key"
          v-model="apiKey"
          type="text"
          class="border border-gray-300 rounded py-2 px-4 focus:outline-none focus:border-blue-300"
        >
      </div>
      <div class="mt-4">
        <button
          class="bg-blue-500 text-white py-2 px-4 rounded hover:bg-blue-600 focus:outline-none"
          @click="submitApiKey"
        >
          Submit
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    visible: {
      type: Boolean,
      default: true
    }
  },
  data () {
    return {
      apiKey: ''
    }
  },
  methods: {
    submitApiKey () {
      this.$emit('submit-api-key', this.apiKey)
      this.$emit('update-visibility', false)
    }
  }
}
</script>
