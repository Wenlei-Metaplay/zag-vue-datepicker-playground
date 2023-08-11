<script setup lang="ts">
import * as datepicker from "@zag-js/date-picker"
import { normalizeProps, useMachine } from "@zag-js/vue"

const [state, send] = useMachine(
    datepicker.machine({
        
    })
)

</script>
