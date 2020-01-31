<template>
  <main class="flex-center-column">
    <h1>Welcome to my app</h1>

    <section class="video">
      <span>Main Video Component</span>
      <span>Current queue: {{ commandQueue.join(" ") }}</span>
      <span>Previous queues: {{ previousCommandQueues.length }}</span>

      <ol
        v-for="(prevQueue, index) in previousCommandQueues"
        :key="index"
        class="video"
      >
        <li v-for="(cmd, cmdIndex) in prevQueue" :key="cmdIndex">
          {{ cmd }}
        </li>
      </ol>
    </section>
    <section class="controls-wrapper">
      <div>Left</div>
      <div>Up/Down</div>
      <div>Right</div>
    </section>
  </main>
</template>

<script lang="ts">
import Vue from "vue"
import { throttle } from "lodash"

interface RCData {
  commandMap: { [s: string]: string }
  commandQueue: CommandQueue
  previousCommandQueues: CommandQueue[]
}

type CommandQueue = string[]

export default Vue.extend({
  data(): RCData {
    return {
      commandMap: {
        ArrowUp: "F",
        ArrowDown: "B",
        ArrowLeft: "L",
        ArrowRight: "R"
      },
      commandQueue: [],
      previousCommandQueues: []
    }
  },

  mounted() {
    window.addEventListener("keydown", this.processKeydown)
  },

  updated() {
    if (this.commandQueue.length > 0) {
      this.processQueue()
    }
  },

  destroyed() {
    window.removeEventListener("keydown", this.processKeydown)
  },

  methods: {
    processKeydown(event: KeyboardEvent): void {
      event.preventDefault()

      if (Object.keys(this.commandMap).includes(event.key)) {
        this.addCommand(this.commandMap[event.key])
      }
    },

    addCommand(command: string): void {
      this.commandQueue = [...this.commandQueue, command]
    },

    processQueue: throttle(function(): void {
      this._processQueue()
    }, 1000),

    _processQueue(): void {
      this.previousCommandQueues.push(this.commandQueue)
      this.commandQueue = []
    }
  }
})
</script>

<style scoped>
.flex-center-column {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.controls-wrapper {
  display: flex;
}

.controls-column {
  display: flex;
  flex-direction: column;
}

.video {
  display: flex;
  flex-direction: column;
  margin-bottom: 10vh;
}
</style>
