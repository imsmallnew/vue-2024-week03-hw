<script setup>
import { defineProps, defineEmits } from 'vue'

const props = defineProps({
  data: Object,
  cart: Object
});
const emit = defineEmits(['add-to-cart'])
const pickedItem = (drink) => {
  return props.cart && props.cart.findIndex((cartItem) => cartItem.name === drink.name) !== -1;
}
</script>

<template>
    <div class="list-group">
        <a
        v-for="drink in data"        
        :key="drink.id"
        :style="{backgroundColor: pickedItem(drink) ? '#EDE8E8' : '#FFFFFF'}"
        href="#"
        class="list-group-item list-group-item-action"
        @click.prevent="emit('add-to-cart', drink)"
        >
        <div class="d-flex w-100 justify-content-between">
            <h5 class="mb-1">{{ drink.name }}</h5>
            <small>${{ drink.price }}</small>
        </div>
        <p class="mb-1">{{ drink.description }}</p>
        </a>
    </div>
</template>