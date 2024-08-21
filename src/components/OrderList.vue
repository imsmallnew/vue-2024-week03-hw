<script setup>
import { defineProps, defineEmits} from 'vue'

const props = defineProps({
    orders: Object,
    filterOrder: Object,
    fullClass: String,
    halfClass: String,
    successClass: String,
    pendingClass: String,
});
const emit = defineEmits(['change-status','cancel-order'])

const shorterOrder = (order) => {
  return order.substring(order.length, 6);
}
const itemSubtotal = (item) => {
  return item.price * item.quantity;
};
</script>

<template>
    <div v-if="orders.length>0"  class="card col-4" v-for="(order,index) in filterOrder" :key="index">
          <div class="card-body">
            <div class="card-title" >
              <div class="row">
                <div class="col-6">
                  <h5 >訂單{{shorterOrder(order.id+"")}}</h5>
                  <p>{{order.orderTime}}</p>
                </div>
                <div class="col-6">
                    <div class="row text-end">
                      <div :class="[order.status ? fullClass : halfClass]" >
                        <button :class="[order.status ? successClass : pendingClass]"  @click="emit('change-status',order)">
                          {{order.status?"已完成":"準備中"}}
                        </button>
                      </div>
                      <div class="col-6" v-if="!order.status">
                        <button class="btn btn-sm btn-danger"  @click="emit('cancel-order',order.id)">
                          {{"取消訂單"}}
                        </button>
                      </div>
                  </div>
                </div>
              </div>
             
              <table class="table">
                <thead>
                  <tr>
                    <th scope="col">品項</th>
                    <th scope="col">數量</th>
                    <th scope="col">小計</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="item in order.cart" :key="item.id">
                    <td>{{ item.name }}</td>
                    <td>{{ item.quantity }}</td>
                    <td>{{ itemSubtotal(item) }}</td>
                  </tr>
                </tbody>
              </table>
              <div class="text-end">
                備註: <span>{{ order.description }}</span>
              </div>
              <div class="text-end">
                <h5>總計: <span>${{ order.sum }}</span></h5>
              </div>
            </div>
          </div>
        </div>
</template>