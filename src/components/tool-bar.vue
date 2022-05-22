<template>
  <div class="info">
    <p class="info_icon">ℹ️</p>
    <p>
      <select :value="modelValue" @input="$emit('update:modelValue', $event.target.value)">
        <option value="en">🇺🇸 English</option>
        <option value="de">🇩🇪 Deutsch</option>
      </select>
    </p>
  </div>
</template>

<script>
export default {
    name: "ToolBar",
    props: ["modelValue"],
    emits: ["update:modelValue"],
}
</script>
