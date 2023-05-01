<template>
  <div
    class="relative space-y-8 px-6 md:px-16 lg:px-24 py-8"
    :class="{ 'pb-36': !chatSettings.aiModerator }"
  >
    <ApiKeyInput
      :visible="apiKeyInputVisible"
      @submit-api-key="setApiKey"
      @update-visibility="toggleApiKeyInput"
    />
    <h1 class="text-3xl font-bold">
      Sanders & Hobbes
    </h1>
    <div>
      <button
        class=" text-white py-2 px-4 -mt-2 fixed top-0 right-6 rounded z-40"
        :class="[settingsVisible ? 'bg-red-500 hover:bg-red-600' : 'bg-blue-500 hover:bg-blue-600']"
        @click="toggleSettings"
      >
        {{ settingsVisible ? "Close" : "Settings" }}
      </button>
    </div>
    <ChatSettings
      ref="chatSettings"
      :visible="settingsVisible"
      :saved-settings="chatSettings"
      @update-settings="updateChatSettings"
      @close-settings="toggleSettings"
    />
    <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
      <AgentInput :agent="agents.agent1" @update-agent="updateAgent(1, $event)" />
      <AgentInput :agent="agents.agent2" @update-agent="updateAgent(2, $event)" />
    </div>
    <TopicInput :topic="topic" @update-topic="updateTopic" />
    <div class="mt-4 sticky top-6">
      <ChatControls
        :ai-moderator="chatSettings.aiModerator"
        :conversationActive="conversationActive"
        @toggle-conversation="toggleConversation"
        @regenerate-response="regenerateResponse"
        @toggle-control="takeControl"
        @reset="resetConversation"
      />
    </div>
    <div class="max-h-full overflow-auto">
      <ChatOutput
        :messages="chatMessages"
        :show-references="chatSettings.references"
      />
    </div>
    <UserPrompt
      :visible="!chatSettings.aiModerator"
      @submit-prompt="handleUserPrompt"
    />
  </div>
</template>

<script>
export default {
  data () {
    return {
      agents: {
        agent1: {
          id: '1',
          name: 'Sanders',
          personality: '',
          messages: []
        },
        agent2: {
          id: '2',
          name: 'Hobbes',
          personality: '',
          messages: []
        },
        agent3: {
          id: 'moderator',
          name: 'Moderator',
          personality: '',
          messages: []
        }
      },
      defaults: {
        agent1: {
          id: '1',
          name: 'Sanders',
          personality: '',
          messages: []
        },
        agent2: {
          id: '2',
          name: 'Hobbes',
          personality: '',
          messages: []
        },
        agent3: {
          id: 'moderator',
          name: 'Moderator',
          personality: '',
          messages: []
        },
        chatSettings: {
          model: 'gpt-3.5-turbo',
          temperature: 1,
          top_p: 1,
          stream: false,
          max_tokens: 1024,
          presence_penalty: 0,
          frequency_penalty: 0,
          consequent_responses: 3,
          aiModerator: false,
          references: true
        }
      },
      topic: '',
      settingsVisible: false,
      apiKeyInputVisible: true,
      chatSettings: {
        model: 'gpt-3.5-turbo',
        temperature: 1,
        top_p: 1,
        stream: false,
        max_tokens: 1024,
        presence_penalty: 0,
        frequency_penalty: 0,
        consequent_responses: 3,
        aiModerator: false,
        references: true
      },
      chatMessages: [],
      conversationActive: false,
      reset: true
    }
  },
  methods: {
    setApiKey (apiKey) {
      this.$axios.defaults.headers.common.Authorization = `Bearer ${apiKey}`
      this.$axios.setToken(apiKey, 'Bearer') // Add this line
    },
    toggleApiKeyInput (visible) {
      this.apiKeyInputVisible = visible
    },
    toggleSettings () {
      this.settingsVisible = !this.settingsVisible
      this.$refs.chatSettings.resetSettings(this.chatSettings)
    },
    updateAgent (agentIndex, updatedAgent) {
      this.$set(this.agents, `agent${agentIndex}`, JSON.parse(JSON.stringify(updatedAgent)))
    },
    updateTopic (updatedTopic) {
      this.topic = updatedTopic
    },
    updateChatSettings (updatedSettings) {
      this.chatSettings = { ...updatedSettings }
    },
    toggleConversation (conversationActive) {
      if (this.reset) {
        this.startConversation()
      } else if (this.conversationActive === false) {
        this.conversationLoop()
      }
      this.conversationActive = !this.conversationActive
    },
    regenerateResponse () {
      // Handle breaking the conversation
    },
    takeControl () {
      this.chatSettings.aiModerator = !this.chatSettings.aiModerator
      this.$refs.chatSettings.$emit('update-settings', this.chatSettings)
      // Halt the next request when taking control
      if (!this.chatSettings.aiModerator) {
        // Stop the next request
      }
    },
    handleUserPrompt (prompt) {
      // Process the user's moderation prompt
    },
    resetConversation () {
      // Reset your app state here
      this.agents.agent1 = this.defaults.agent1
      this.agents.agent2 = this.defaults.agent2
      this.agents.agent3 = this.defaults.agent3
      this.topic = ''
      this.chatMessages = []
      this.conversationActive = false
      this.reset = true
    },
    async startConversation () {
      console.log('start')
      this.agents.agent1.messages = [
        { role: this.chatSettings.model === 'gpt-4' ? 'system' : 'user', content: `From now on You are acting as ${this.agents.agent1.name}, ${this.agents.agent1.personality}. Your opponent is ${this.agents.agent2.name}. You are debating on this topic: ${this.topic}. In your responses follow your arguments with a reference list of sources cited in that response if available, title this list "References:", start your response with "${this.agents.agent1.name}: ". Stand firm on your arguments but try to answer concisely.` },
        { role: 'user', content: `${this.agents.agent1.name}: ` }
      ]
      this.agents.agent2.messages = [
        { role: this.chatSettings.model === 'gpt-4' ? 'system' : 'user', content: `From now on You are acting as You are ${this.agents.agent2.name}, ${this.agents.agent2.personality}. Your opponent is ${this.agents.agent1.name}. You are debating on this topic: ${this.topic}. In your responses follow your arguments with a reference list of sources cited in that response if available, title this list "References:", start your response with "${this.agents.agent2.name}: ". Stand firm on your arguments but try to answer concisely.` }
      ]
      this.agents.agent3.messages = [
        { role: this.chatSettings.model === 'gpt-4' ? 'system' : 'user', content: `From now on You are acting as a moderator in public debates. ${this.agents.agent1.name} and ${this.agents.agent1.name} are debating on this topic: ${this.topic}. Control the flow of discussion so the debaters would not go of topic or ask specifying questions.` }
      ]
      // Prompt the first agent
      const agent1Response = await this.promptAgent(this.agents.agent1)
      this.addResponseToConversation(this.agents.agent1, agent1Response.choices[0].message.content)

      // Prompt the second agent
      const agent2Response = await this.promptAgent(this.agents.agent2)
      this.addResponseToConversation(this.agents.agent2, agent2Response.choices[0].message.content)

      // Continue the conversation
      this.conversationActive = true
      this.reset = false
      console.log('start_loop')
      this.conversationLoop()
    },
    async promptAgent (agent) {
      // const opponent = (agent === this.agents.agent1) ? this.agents.agent2.name : this.agents.agent1.name
      const prompt = {
        model: this.chatSettings.model,
        messages: agent.messages,
        temperature: this.chatSettings.temperature,
        top_p: this.chatSettings.top_p,
        stream: this.chatSettings.stream,
        max_tokens: this.chatSettings.max_tokens,
        presence_penalty: this.chatSettings.presence_penalty,
        frequency_penalty: this.chatSettings.frequency_penalty,
        logit_bias: this.chatSettings.logit_bias,
        user: 'Hobbes-test-user'
      }
      // Call the OpenAI API with the constructed prompt
      console.log('prompt: ', { ...prompt })
      const response = await this.callOpenAI(prompt)

      // Return the response
      return response
    },
    addResponseToConversation (talkingAgent, response) {
      if (response.length > 0) {
        const messageContent = response.trim()
        const referenceRegex = /References:(.*)$/s
        const referenceMatch = messageContent.match(referenceRegex)

        let references = []
        if (referenceMatch) {
          references = referenceMatch[1].trim().split(/\n\s*\d+\.\s*/)
          references.shift() // Remove the first empty item caused by the split
        }

        const messageWithoutReferences = messageContent.replace(referenceRegex, '').trim()

        this.chatMessages.push({
          authorId: talkingAgent.id,
          role: 'Assistant',
          sender: talkingAgent.name,
          text: messageWithoutReferences,
          references
        })

        for (const agent in this.agents) {
          if (this.agents[agent] !== talkingAgent.id) {
            this.agents[agent].messages.push({ role: 'user', content: messageContent })
          } else {
            this.agents[agent].messages.push({ role: 'assistant', content: messageContent })
          }
        }
      }
    },
    async conversationLoop () {
      for (let i = 0; i < this.chatSettings.consequent_responses; i++) {
        // Prompt the first agent
        if (this.conversationActive === true) {
          const agent1Response = await this.promptAgent(this.agents.agent1)
          this.addResponseToConversation(this.agents.agent1, agent1Response.choices[0].message.content)

          // Prompt the second agent
          const agent2Response = await this.promptAgent(this.agents.agent2)
          this.addResponseToConversation(this.agents.agent2, agent2Response.choices[0].message.content)
        }
      }
      this.conversationActive = false
    },
    async callOpenAI (prompt) {
      const apiUrl = '/chat/completions'

      try {
        const response = await this.$axios.$post(apiUrl, prompt)
        console.log('response: ', { ...response })
        return response
      } catch (error) {
        console.error('Error calling OpenAI API:', error)
        return null
      }
    }
  }
}
</script>
