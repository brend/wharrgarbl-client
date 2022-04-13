<template>
    <div class="historyView">
        <ul v-if="items.length" class="historyList">
            <li v-for="(item, i) in items" :key="i">
                {{item.text}}
            </li>
        </ul>
        <p v-if="!items.length">Guess History</p>
    </div>
</template>

<script>
export default {
    name: 'HistoryView',
    props: ['items'],
}
</script>
