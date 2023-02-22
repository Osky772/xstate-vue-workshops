<script setup>
import {createMachine, assign} from "xstate";
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
        INCREMENT: {
          target: "loading",
        },
      },
    },
    loading: {
      invoke: {
        src: "invokeIncrementPromise",
        id: "incrementPromise",
        onDone: {
          target: "normal",
          actions: "increment",
        },
        onError: {
          target: "error",
        },
      },
    },
    error: {
      on: {
        INCREMENT: {
          target: "loading",
        },
      },
    },
  },
  predictableActionArguments: true,
  preserveActionOrder: true,
}, {
  actions: {
    increment: assign({
      count: (context) => context.count + 1,
    }),
  },
  services: {
    invokeIncrementPromise: () => {
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
  <section :class="[state.matches('error') && 'error']"> 
    <h1>{{state.matches('loading') ? 'Loading...' : 'Counter app'}}</h1>
    <p>State: {{state.value}}</p>
    <p>Count: {{state.context.count}}</p>
    <button @click="send('INCREMENT')">Increment</button>
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
