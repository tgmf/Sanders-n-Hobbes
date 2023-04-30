<template>
  <transition name="slide">
    <div v-if="visible" class="fixed bottom-0 left-0 w-full bg-white p-4 border-t">
      <form @submit.prevent="submitPrompt">
        <div class="flex items-center">
          <textarea
            v-model="userPrompt"
            class="flex-grow p-2 border border-gray-300 rounded"
            rows="3"
            placeholder="Enter your moderation prompt"
          />
          <button
            type="submit"
            class="ml-4 bg-green-500 text-white py-2 px-4 rounded hover:bg-green-600"
          >
            Submit
          </button>
        </div>
      </form>
    </div>
  </transition>
</template>

<script>
export default {
  props: {
    visible: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      userPrompt: ''
    }
  },
  methods: {
    submitPrompt () {
      this.$emit('submit-prompt', this.userPrompt)
      this.userPrompt = ''
    }
  }
}
</script>

<style>
  .slide-enter-active,
  .slide-leave-active {
    transition: all 0.3s ease;
  }
  .slide-enter,
  .slide-leave-to {
    transform: translateY(100%);
  }
</style>
