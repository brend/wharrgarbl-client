<template>
  <div class="info">
    <p class="info_icon" 
      @mouseenter="showInfo = true"
      @mouseleave="showInfo = false"
      @click="showInfo = !showInfo"
    >
      <span>ℹ️</span>
      <popup-box 
        v-model="showInfo" 
        text="How to play: Try to guess a 5-letter word. The word will contain no duplicate letters (e.g. the E in STEEL). You can guess as many times as you like. Try to break your record!"
      ></popup-box>
    </p>
    <p>
      <select :value="modelValue" @input="$emit('update:modelValue', $event.target.value)">
        <option value="en">🇺🇸 English</option>
        <option value="de">🇩🇪 Deutsch</option>
      </select>
    </p>
  </div>
</template>

<script>
import PopupBox from "./PopupBox";

export default {
    name: "ToolBar",
    props: ["modelValue"],
    emits: ["update:modelValue"],
    components: {PopupBox},
    data() {
      return {
        showInfo: false,
      };
    },
}
</script>
