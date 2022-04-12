<template>
  <span :class="color()">{{letter}}</span>
</template>

<script>
export default {
    name: 'LetterBox',
    props: ['letter', 'hints'],
    methods: {
        color() {
            const hint = this.hints?.[this.letter];

            if (!hint) {
                return {};
            }

            if (hint['no']) {
                return {no: true};
            } else if (hint['yes']) {
                return {yes: true};
            } else if (hint['elsewhere']) {
                return {elsewhere: true};
            } else {
                return {error: true};
            }
        },
    }
}
</script>

<style>

</style>