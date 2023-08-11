<script setup lang="ts">
  import * as combobox from "@zag-js/combobox"
  import { normalizeProps, useMachine } from "@zag-js/vue"
  import { computed, ref } from "vue"

  type ComboBox = {
    label: string;
    value: string;
    disabled?: boolean;
  };
  const divisionsInCurrentlySelectedRank = 100000; // replace with actual value
  const maxVisibleOptions = 100; // Maximum number of options to be rendered in the DOM
  
  // Function to find relevant divisions based on user input
  const findRelevantDivisions = (input: string): ComboBox[] => {
    // Implement your algorithm here to find relevant divisions based on user input
    // For now, we just return the first 'maxVisibleOptions' divisions
    return Array.from({length: maxVisibleOptions}, (_, i) => ({ label: `Division ${i}`, value: `${i}` }));
  };
  
  // Initial comboboxData is empty
  const comboboxData: ComboBox[] = [];
  const options = ref(comboboxData)

  const [state, send] = useMachine(
    combobox.machine({
      id: "combobox",
      onOpen() {
        options.value = Array.from({length: maxVisibleOptions}, (_, i) => ({ label: `Division ${i}`, value: `${i}` }));
      },
      onInputChange({ value }) {
        const index = parseInt(value);
        if (!isNaN(index) && index >= 0 && index < divisionsInCurrentlySelectedRank) {
          options.value = [{ label: `Division ${index}`, value: `${index}` }];
        } else {
          options.value = []
        }
      },
    }),
  )

  const api = computed(() =>
    combobox.connect(state.value, send, normalizeProps),
  )
</script>

<template>
  <div v-bind="api.rootProps">
    <label v-bind="api.labelProps">Select Division</label>

    <div v-bind="api.controlProps">
      <input v-bind="api.inputProps" />
      <button v-bind="api.triggerProps">▼</button>
    </div>
  </div>
  <div v-bind="api.positionerProps">
    <ul v-if="options.length > 0" v-bind="api.contentProps">
      <li
        v-for="(item, index) in options"
        :key="item.value"
        v-bind="api.getOptionProps({
          label: item.label,
          value: item.value,
          index,
          disabled: item.disabled,
        })"
      >
        {{item.label}}
      </li>
    </ul>
  </div>
</template>