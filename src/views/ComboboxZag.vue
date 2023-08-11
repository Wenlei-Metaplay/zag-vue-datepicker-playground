<script setup lang="ts">
  import * as combobox from "@zag-js/combobox"
  import { normalizeProps, useMachine } from "@zag-js/vue"
  import { computed, ref } from "vue"

  type ComboBox = {
    /**
     * The label of the option. Used to populate the combobox's input when selected
     */
    label: string;
    /**
     * The actual value of the option
     */
    value: string;
    /**
    * Whether the option is disabled
    */
    disabled?: boolean;
};

  const comboboxData: ComboBox[] = [
  { label: "Division 0 of Bronze", value: "0", disabled: false },
  //...
]

  const options = ref(comboboxData)

  const [state, send] = useMachine(
    combobox.machine({
      id: "combobox",
      onOpen() {
        options.value = comboboxData
      },
      onInputChange({ value }) {
        const filtered = comboboxData.filter((item) =>
          item.label.toLowerCase().includes(value.toLowerCase()),
        )
        options.value = filtered.length > 0 ? filtered : comboboxData
      },
    }),
  )

  const api = computed(() =>
    combobox.connect(state.value, send, normalizeProps),
  )
</script>

<template>
  <div v-bind="api.rootProps">
    <label v-bind="api.labelProps">Select country</label>

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