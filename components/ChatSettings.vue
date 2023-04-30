<template>
  <aside
    :class="{ 'translate-x-0 shadow-lg': visible, 'translate-x-full': !visible }"
    class="bg-white p-6 w-96 fixed top-0 right-0 h-full transform transition duration-300 ease-in-out overflow-auto z-30"
    style="margin-top: 0;"
  >
    <h3 class="text-xl font-semibold mb-8">
      Chat Settings
    </h3>
    <div class="flex flex-col space-y-2">
      <label
        for="model"
        class="text-sm font-medium"
      >
        Model:
      </label>
      <select
        id="model"
        v-model="settings.model"
        class="border-2 border-gray-300 rounded px-3 py-2 mb-4"
      >
        <option value="gpt-4">
          GPT-4
        </option>
        <option value="gpt-4-32k">
          GPT-4-32k
        </option>
        <option value="gpt-3.5-turbo">
          GPT-3.5-Turbo
        </option>
      </select>
    </div>

    <div v-for="(param, index) in parameters" :key="index" class="flex flex-col space-y-2 mb-4">
      <label
        :for="param.name"
        class="text-sm font-medium mt-4"
      >
        {{ param.label }}: {{ settings[param.name] }}
      </label>
      <input
        :id="param.name"
        v-model.number="settings[param.name]"
        :type="param.type"
        :min="param.min"
        :max="param.max"
        :step="param.step"
        class="border-2 border-gray-300 rounded px-3 py-2"
      >
    </div>

    <div class="flex items-center space-x-2 mb-8">
      <input id="stream" v-model="settings.stream" type="checkbox">
      <label for="stream" class="text-sm font-medium">Stream</label>
    </div>
    <div class="flex items-center space-x-2 mb-8">
      <input id="ai-moderator" v-model="settings.aiModerator" type="checkbox">
      <label for="ai-moderator" class="text-sm font-medium">AI Moderator</label>
    </div>
    <div class="flex items-center space-x-2 mb-8">
      <input id="show-references" v-model="settings.references" type="checkbox">
      <label for="show-references" class="text-sm font-medium">Show References</label>
    </div>

    <div class="flex justify-between mt-4">
      <button
        class="bg-blue-500 text-white py-2 px-4 rounded hover:bg-blue-600"
        @click="saveSettings"
      >
        Save
      </button>
    </div>
  </aside>
</template>

<script>
export default {
  props: {
    visible: {
      type: Boolean,
      default: false
    },
    savedSettings: {
      type: Object,
      default: () => ({
        model: 'gpt-4',
        temperature: 1,
        top_p: 1,
        stream: false,
        max_tokens: 1024,
        presence_penalty: 0,
        frequency_penalty: 0,
        consequent_responses: 3,
        aiModerator: true,
        references: true
      })
    }
  },
  data () {
    return {
      settings: { ...this.savedSettings },
      parameters: [
        {
          name: 'temperature',
          label: 'Temperature',
          type: 'range',
          min: 0,
          max: 2,
          step: 0.01
        },
        {
          name: 'top_p',
          label: 'Top P',
          type: 'range',
          min: 0,
          max: 1,
          step: 0.01
        },
        {
          name: 'max_tokens',
          label: 'Max Tokens',
          type: 'number',
          min: 1,
          max: 2048,
          step: 1
        },
        {
          name: 'presence_penalty',
          label: 'Presence Penalty',
          type: 'range',
          min: -2,
          max: 2,
          step: 0.01
        },
        {
          name: 'frequency_penalty',
          label: 'Frequency Penalty',
          type: 'range',
          min: -2,
          max: 2,
          step: 0.01
        },
        {
          name: 'consequent_responses',
          label: 'Consequent Responses',
          type: 'range',
          min: 1,
          max: 10,
          step: 1
        }
      ]
    }
  },
  methods: {
    saveSettings () {
      this.$emit('update-settings', this.settings)
      this.$emit('close-settings')
    },
    closeSettings () {
      this.$emit('close-settings')
    },
    resetSettings (actualSettings) {
      this.settings = { ...actualSettings }
    }
  }
}
</script>
