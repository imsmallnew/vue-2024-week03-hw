<script setup>
import { defineProps, defineEmits} from 'vue'

const props = defineProps({
    orders: Object,
    preparedOrder: Object,
    doneOrder: Object,
    topDrinks: Object,
    totalValue: Number,
});
const emit = defineEmits(['change-tab'])

</script>

<template>
    <div v-if="!orders.length>0" class="alert alert-secondary text-center" role="alert">
        尚未建立訂單
    </div>

    <div v-if="orders.length>0">
        <div class="row mb-3 text-primary">
        <div class="col-6">
            <div class="row">
            <div class="col-2 text-center">
            <a class="nav-link" href="#" @click.prevent="emit('change-tab','1')">全部訂單:<h2>{{orders.length}}</h2></a>
            </div>  
            <div class="col-2 text-center" >
                <a class="nav-link" href="#"  @click.prevent="emit('change-tab','2')">準備中訂單:<h2>{{preparedOrder.length}}</h2></a>
            </div>
            <div class="col-2 text-center">
                <a class="nav-link" href="#" @click.prevent="emit('change-tab','3')">已完成訂單:<h2>{{doneOrder.length}}</h2></a>
            </div>
            <div class="col-3 text-center text-danger">
                <a class="nav-link" href="#">今日銷售總金額:<h2>{{totalValue}}</h2></a> <!--完成訂單後才會加入總銷售金額-->
            </div>
            </div>
        </div>
        <div class="col-6">
            <div class="row text-success">
            <div>TOP 3 銷售商品:</div>
            <div v-for="(item,index) in topDrinks" :key="index" class="col-4">
                <h3>{{item.name}}({{item.totalQuantity}})</h3>
            </div>
            </div>
        </div>
        </div>
    </div>
</template>