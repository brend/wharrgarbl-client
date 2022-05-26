<template>
  <div class="info">
    <p class="info_icon" 
      @mouseenter="showRules = true"
      @mouseleave="showRules = false"
      @click="showRules = !showRules"
    >
      <span>ℹ️</span>
      <popup-box 
        v-model="showRules" 
        text="How to play: 
        
        Try to guess a 5-letter word. 

        No duplicate letters allowed (e.g. the E in STEEL). 

        You can guess as many times as you like. 
        
        You can reset the game any time you like, and try to guess a new word. 
        
        Try to break your record!"
      ></popup-box>
    </p>
    <p class="info_icon"
      @mouseenter="showResetInfo = true"
      @mouseleave="showResetInfo = false"
      @click="$emit('reset')"
    >
      <span>🔄</span>
      <popup-box v-model="showResetInfo" text="reset game and guess a new word"></popup-box>
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
    emits: ["update:modelValue", "reset"],
    components: {PopupBox},
    data() {
      return {
        showRules: false,
        showResetInfo: false,
      };
    },
}
</script>
