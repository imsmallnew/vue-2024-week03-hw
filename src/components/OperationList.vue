<script setup>
import { defineProps, defineEmits, defineModel } from 'vue'

const props = defineProps({
  cart: Object,
  sum: Number,
});
const emit = defineEmits(['remove-from-cart','update-cart','create-order'])
const description = defineModel('description');
const itemSubtotal = (item) => {
  return item.price * item.quantity;
};
</script>

<template>
    <table class="table">
        <thead>
        <tr>
            <th scope="col" width="50">操作</th>
            <th scope="col">品項</th>
            <th scope="col">描述</th>
            <th scope="col" width="90">數量</th>
            <th scope="col">單價</th>
            <th scope="col">小計</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="item in cart" :key="item.id">
            <td>
            <button
                type="button"
                class="btn btn-sm"
                @click="emit('remove-from-cart', item.id)"
            >
                x
            </button>
            </td>
            <td>{{ item.name }}</td>
            <td><small>{{ item.description }}</small></td>
            <td>
            <select
                class="form-select"
                v-model="item.quantity"
                @change="emit('update-cart', item)"
            >
                <option v-for="n in 10" :key="n" :value="n">{{ n }}</option>
            </select>
            </td>
            <td>{{ item.price }}</td>
            <td>{{ itemSubtotal(item) }}</td>
        </tr>
        </tbody>
    </table>
    <div v-if="cart.length === 0" class="alert alert-primary text-center" role="alert">
        請選擇商品
    </div>
    <div v-else>
        <div class="text-end mb-3">
        <h5>總計: <span>${{ sum }}</span></h5>
        </div>
        <textarea
        class="form-control mb-3"
        rows="3"
        placeholder="備註"
        v-model="description"
        ></textarea>
        <div class="text-end">
        <button class="btn btn-primary" @click.prevent="emit('create-order')">
            送出
        </button>
        </div>
    </div>
</template>