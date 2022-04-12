<template>
    <letter-box 
        v-for="(c, ci) in word.split('')" 
        :key="ci"
        :letter="c"
        :hints="hints"
    ></letter-box>
</template>

<script>
import LetterBox from './LetterBox.vue';

export default {
    name: 'WordRow',
    components: {LetterBox},
    props: ['word', 'hints'],
}
</script>

<style>

</style>