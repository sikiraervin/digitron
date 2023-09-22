<script>
export default {
    name: 'NumBtn',
    props: [
        'addToExpression',
        'broj'
    ],
}
</script>
<template>
    <button 
        class="digitron-element_base" 
        :class="broj === 0 ? 'digitron-button__zero' : 'digitron-button'"
        @click="addToExpression(broj)"
    >
        {{ broj }}
    </button>
</template>