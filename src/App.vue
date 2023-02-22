<script setup>
import {createMachine} from "xstate";
import {useMachine} from "@xstate/vue";

const counterMachine = createMachine({
  id: "Counter",
  initial: "normal",
  context: {
    count: 0
  },
  states: {
    normal: {
      on: {
        DECREMENT: {
          target: "loading.decrement",
        },
        INCREMENT: {
          target: "loading.increment",
        },
      },
    },
    loading: {
      states: {
        increment: {
          invoke: {
            src: "invokeIncrementPromise",
            id: "invokeIncrementPromise",
            onDone: {
              target: "#Counter.normal",
              actions: [
                (context, event) => {
                  context.count++;
                },
              ],
            },
            onError: {
              target: "#Counter.error",
            },
          },
        },
        decrement: {
          invoke: {
            src: "invokeDecrementPromise",
            id: "invokeDecrementPromise",
            onDone: {
              target: "#Counter.normal",
              actions: [
                (context, event) => {
                  context.count--;
                },
              ],
            },
            onError: {
              target: "#Counter.error",
            },
          },
        },
      }
    },
    error: {
      on: {
        INCREMENT: {
          target: "loading.increment",
        },
        DECREMENT: {
          target: "loading.decrement",
        },
      },
    },
  },
  predictableActionArguments: true,
  preserveActionOrder: true,
}, {
  services: {
    invokeIncrementPromise: (context) => {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          const randomSuccess = Math.random() > 0.5;
          if (randomSuccess) {
            resolve();
          } else {
            reject();
          }
        }, 1000);
      });
    },
    invokeDecrementPromise: (context) => {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          const randomSuccess = Math.random() > 0.5;
          if (randomSuccess) {
            resolve();
          } else {
            reject();
          }
        }, 1000);
      });
    },
  }
});

const {state, send} = useMachine(counterMachine, {devTools: true});

</script>

<template>
  <section :class="[state.matches('error') && '.error']"> 
    <h1>Counter app</h1>
    <p>State: {{state.value}}</p>
    <p>Count: {{state.context.count}}</p>
    <button @click="send('INCREMENT')">Increment</button>
    <button @click="send('DECREMENT')">Decrement</button>
  </section>
</template>

<style scoped>
.state {
  font-size: 2rem;
}
.error {
  color: red;
}
</style>
