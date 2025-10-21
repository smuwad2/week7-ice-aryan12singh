<script>
    export default { 
        // Add code in appropriate places
        data() {
            return {
                items: ['keyboard', 'mouse', 'iPhone', 'macbook', 'adapter']
                newItem: ''  // For storing the text input value
            }
            
        }
          methods: {
            deleteItem(index) {
            // Remove item at specific index
            this.items.splice(index, 1);
            },
            addItem() {
            // Only add if newItem is not empty
            if (this.newItem.trim() !== '') {
                this.items.push(this.newItem);
                this.newItem = '';  // Clear the input after adding
            }
            }
        }
    }
</script>

<template>

    <h2>Shopping Cart</h2>
    <!-- TODO: Add Code Here -->
     <ul>
        <li v-for="(item, index) in items" :key="index">
        {{ item }}
        <button @click="deleteItem(index)">Delete!</button>
        </li>
     </ul>
    
     <input v-model="newItem" type="text" placeholder="Enter item">
     <button @click="addItem">Add!</button>
</template>
