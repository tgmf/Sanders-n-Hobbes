<template>
  <div class="bg-white py-6 rounded shadow space-y-6 overflow-y-auto max-h-[70vh]">
    <div v-for="message in messages" :key="message.id">
      <div class="font-bold">
        {{ message.sender }}:
      </div>
      <div>
        {{ message.text }}
      </div>
      <details v-if="message.references.length && showReferences" class="text-sm text-blue-500 mt-2">
        <summary>References</summary>
        <ul>
          <li v-for="reference in message.references" :key="reference" class="mt-2" v-html="linkify(reference)" />
        </ul>
      </details>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    messages: {
      type: Array,
      default: () => []
    },
    showReferences: {
      type: Boolean,
      deafult: true
    }
  },
  methods: {
    linkify (inputText) {
      const urlPattern = /(\b(https?|ftp):\/\/[-A-Z0-9+&@#\/%?=~_|!:,.;]*[-A-Z0-9+&@#\/%=~_|])/gi //eslint-disable-line
      return inputText.replace(urlPattern, '<a href="$1" target="_blank" rel="noopener noreferrer" class="underline">$1</a>')
    }
  }
}
</script>
