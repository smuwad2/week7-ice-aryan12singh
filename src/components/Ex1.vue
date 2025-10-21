<script>
    export default {
    data() {
        return {
        x: 10,
        y: 2,
        operators: ['+', '-', '*', '/', '%'],
        selectedOp: '+'
        }
    },
    computed: {
        result() {
        switch(this.selectedOp) {
            case '+': return this.x + this.y;
            case '-': return this.x - this.y;
            case '*': return this.x * this.y;
            case '/': return this.x / this.y;
            case '%': return this.x % this.y;
            default: return 0;
        }
        }
    }
    }
</script>

<template>
    <p>x <input v-model.number="x"></p>
    <select v-model="selectedOp">
        <option v-for="op in operators" >{{ op }}</option>
    </select>
    <p>y <input v-model.number="y"></p>
    
    <p>---------------------</p>

    <p>= {{result}}</p>
</template>

<style scoped>
    p, input { font-family: monospace; }
    p { white-space: pre; }

</style>
